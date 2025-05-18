---
title: "The Surgical Data Recovery - How We Restored 50,382 Records"
date: 2025-01-12
categories: [web-development, databases]
tags: [django, postgresql, database-recovery, python]
---

# Part 2: The Surgical Data Recovery - How We Restored 50,382 Records Without Downtime

## The 5-Phase Recovery Operation

### Phase 1: Isolating the Backup Environment

First, we created an isolated recovery instance from our most recent backup:

```bash
# Create recovery database from backup
pg_restore --create --dbname=recovery_db_temp /backups/hourly/latest.dump

# Rename with timestamp for tracking
psql -c "ALTER DATABASE recovery_db_temp RENAME TO recovery_db_incident_20250108;"
```

Then configured Django to access both databases:

```python
# settings.py
DATABASES = {
    'default': {  # Production database
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'production_db',
        'USER': 'db_user',
        'PASSWORD': 'securepassword',
        'HOST': 'db-primary',
        'PORT': '5432',
    },
    'recovery': {  # Backup database
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'recovery_db_incident_20250108',
        'USER': 'db_user',
        'PASSWORD': 'securepassword',
        'HOST': 'db-recovery',
        'PORT': '5432',
    }
}
```

{% include tip.html content="**Pro Tip**: Use separate database hosts for recovery to avoid production performance impacts" %}

### Phase 2: Forensic Analysis with Django ORM

We built a comparison tool to identify exactly what was missing:

```python
from django.db.models import Q
from django.db import connections

def find_deleted_records(model, backup_filter=None):
    """Compare live vs backup databases"""
    backup_filter = backup_filter or Q()
    
    with connections['recovery'].cursor() as cursor:
        cursor.execute(f"SELECT COUNT(*) FROM {model._meta.db_table}")
        backup_total = cursor.fetchone()[0]
    
    live_ids = set(model.objects.using('default')
                  .values_list('id', flat=True))
    backup_ids = set(model.objects.using('recovery')
                    .filter(backup_filter)
                    .values_list('id', flat=True))
    
    return {
        'deleted': backup_ids - live_ids,
        'backup_total': backup_total,
        'live_total': len(live_ids)
    }

# Usage:
order_analysis = find_deleted_records(Order, Q(is_test=True))
```

**Discovered Impact:**

| Table        | Records Lost | Backup Total | Live Total | Data Lost |
|-------------|-------------:|-------------:|-----------:|----------:|
| **Orders**   | 1,237       | 58,921      | 57,684    | 2.1%     |
| **Payments** | 8,642       | 192,453     | 183,811   | 4.5%     |
| **Refunds**  | 12,109      | 84,229      | 72,120    | 14.4%    |
| **Audit Logs** | 28,394    | 1,203,442   | 1,175,048 | 2.4%     |

### Phase 3: The Collector Pattern Implementation

We enhanced Django's deletion collector for recovery purposes:

```python
from django.contrib.admin.utils import NestedObjects
from django.db import transaction

class RecoveryCollector(NestedObjects):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self._prepared_instances = []
    
    def prepare(self, instance):
        """Prepares instances for bulk creation"""
        if hasattr(instance, 'prepare_for_recovery'):
            instance.prepare_for_recovery()
        self._prepared_instances.append(instance)

def recover_object_graph(original_pks, model, batch_size=500):
    collector = RecoveryCollector(using='recovery')
    collector.collect(model.objects.using('recovery')
                    .filter(pk__in=original_pks))
    
    with transaction.atomic(using='default'):
        # Disable triggers and constraints
        with connections['default'].cursor() as cursor:
            cursor.execute("SET session_replication_role = replica;")
        
        # Process in batches to avoid memory issues
        for i in range(0, len(collector._prepared_instances), batch_size):
            batch = collector._prepared_instances[i:i+batch_size]
            for instance in batch:
                instance.save(using='default', force_insert=True)
        
        # Re-enable constraints
        with connections['default'].cursor() as cursor:
            cursor.execute("SET session_replication_role = DEFAULT;")
            cursor.execute("ANALYZE;")  # Update statistics
```

{% include warning.html content="**Critical**: Always test recovery procedures with a recent backup before an actual emergency!" %}

### Phase 4: Handling Special Cases

**M2M Through Models:**

```python
def recover_m2m_relations(through_model, source_field, target_field):
    existing = set(through_model.objects.using('default')
                  .values_list(f'{source_field}_id', f'{target_field}_id'))
    
    new_relations = []
    for relation in through_model.objects.using('recovery').all():
        key = (getattr(relation, f'{source_field}_id'),
               getattr(relation, f'{target_field}_id'))
        if key not in existing:
            new_relations.append(relation)
    
    through_model.objects.using('default').bulk_create(
        new_relations,
        batch_size=1000,
        ignore_conflicts=True
    )

# Usage:
recover_m2m_relations(Order.tags.through, 'order', 'tag')
```

**Polymorphic Models:**

```python
def recover_polymorphic(model, pks):
    for instance in model.objects.using('recovery').filter(pk__in=pks):
        # Save child record first
        concrete = instance.get_real_instance()
        concrete.save(using='default')
        
        # Then save base record
        instance.save(using='default')
```

### Phase 5: Post-Recovery Verification

**Comprehensive Integrity Checks:**

```python
def verify_integrity():
    checks = [
        # Orders with missing customers
        lambda: Order.objects.using('default')
                  .filter(customer__isnull=True).count(),
                  
        # Payments with missing orders
        lambda: Payment.objects.using('default')
                    .filter(order__isnull=True).count(),
                    
        # Refunds with missing payments
        lambda: Refund.objects.using('default')
                   .filter(payment__isnull=True).count()
    ]
    
    errors = []
    for i, check in enumerate(checks):
        count = check()
        if count > 0:
            errors.append(f"Check {i+1} failed: {count} orphans")
    
    if errors:
        raise IntegrityError("\n".join(errors))
```

**Sequence Resynchronization:**

```python
def resync_sequences():
    models = [Order, Payment, Refund, AuditLog]
    
    with connections['default'].cursor() as cursor:
        for model in models:
            table = model._meta.db_table
            cursor.execute(
                f"SELECT setval('{table}_id_seq', "
                f"(SELECT MAX(id) FROM {table}));"
            )
```

## Critical Lessons Learned

1. **The Recovery Toolkit We Created:**
   - Automated backup verification scripts
   - Dry-run recovery mode
   - Relationship visualization tool
   - Integrity check framework

2. **New Safety Protocols:**
   ```python
   class ProtectedDeleteQuerySet(models.QuerySet):
       def delete(self):
           if self.count() > SAFE_DELETE_THRESHOLD:
               raise MassDeletionError(
                   f"Attempted to delete {self.count()} records. "
                   "Use explicit batch deletion instead."
               )
           return super().delete()
   
   class Order(models.Model):
       objects = ProtectedDeleteQuerySet.as_manager()
   ```

3. **Monitoring Improvements:**
   ```python
   @receiver(pre_delete)
   def monitor_deletions(sender, instance, **kwargs):
       if not hasattr(monitor_deletions, 'counts'):
           monitor_deletions.counts = defaultdict(int)
       
       monitor_deletions.counts[sender.__name__] += 1
       
       if monitor_deletions.counts[sender.__name__] > 100:
           trigger_alert(
               f"Mass deletion in progress: "
               f"{monitor_deletions.counts[sender.__name__]} "
               f"{sender.__name__} records deleted"
           )
   ```

## Coming in Part 3: Building a Disaster-Proof Architecture

**Preview of what we'll cover:**
- Zero-downtime migration strategies
- Database-level protection mechanisms
- Automated recovery testing framework
- Chaos engineering for resilience testing
