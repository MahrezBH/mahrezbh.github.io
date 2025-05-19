---
title: "The Day We Lost Customer Data – A Django CASCADE Horror Story"
date: 2025-01-08
categories: [web-development, databases]
tags: [django, postgresql, database-recovery, python]
series: "Django Data Recovery Chronicles"
series_index: 1
---

# Part 1: The Day We Lost Customer Data – A Django CASCADE Horror Story  

## It Started as a Routine Cleanup  

I was performing standard database maintenance on our e-commerce platform. We had accumulated thousands of test orders over months of development, and it was time to clean them up.  

```python
# The innocent-looking command that started it all
Order.objects.filter(is_test=True).delete()
```

{% include tip.html content="**Always check relationships** before mass deletions. Run `Order._meta.get_fields()` to see all related models." %}

## The Moment Panic Set In  

Within **11 seconds** (we timed it later):  
- 📞 23 customer support calls about missing orders  
- 💸 $18,200 in processed payments disappeared from reports  
- 📉 Dashboard showed **50,382 records** vaporized  

{% include warning.html content="CASCADE deletions are **atomic** - once started, they can't be stopped mid-execution!" %}

## How a Simple Delete Became a Disaster  

### The Silent Data Killer: on_delete=CASCADE  

```python
# Our dangerous relationship definitions
class Order(models.Model):
    customer = models.ForeignKey(Customer, on_delete=models.CASCADE)  # 🚨

class Payment(models.Model):
    order = models.ForeignKey(Order, on_delete=models.CASCADE)  # 💥

class Refund(models.Model):
    payment = models.ForeignKey(Payment, on_delete=models.CASCADE)  # 🔥
```

The cascade effect timeline:
1. **00:00:00** - 1,237 test orders deleted  
2. **00:00:03** - 8,642 related payments gone  
3. **00:00:07** - 12,109 refunds vanished  
4. **00:00:11** - 28,394 audit logs erased  

{% include note.html content="Django doesn't show deletion previews. Use `queryset.count()` and `collector.collect()` to estimate impact." %}

### What We Didn't Anticipate  
- **Reverse relations**: `Customer.order_set` was being used in analytics  
- **M2M through models**: `Order.tags` had a hidden cascade effect  
- **Signals**: `post_delete` handlers were archiving data (then deleting it)  

## First Response: Damage Assessment  

1. **Emergency protocol**:  
   ```bash
   ./manage.py maintenance_mode on  # Custom command
   ```
2. **Backup verification**:  
   ```sql
   SELECT pg_size_pretty(pg_database_size('backup_db'));  # 42GB - good
   ```
3. **Forensic analysis**:  
   ```python
   from django.db.models.signals import pre_delete
   print(connections['default'].queries[-20:])  # See recent SQL
   ```

{% include pro-tip.html content="Keep a **read-only replica** for forensic analysis during disasters." %}

## Why We Couldn't Just Restore From Backup  

| Option | Risk | Time Estimate |
|--------|------|---------------|
| Full restore | Lose 1 hour of real orders | 2+ hours |
| Partial restore | FK violations | Unknown |
| Surgical recovery | Complex but precise | 90 minutes |

{% include warning.html content="PostgreSQL's `pg_restore --data-only` still checks constraints!" %}

## Coming in Part 2: The Surgical Recovery  

**What to expect**:  
1. 💻 Creating a parallel recovery environment  
2. 🔎 Using Django's `deletion.Collector` properly  
3. ⚡ Batch processing with `iterator()`  
4. � Handling M2M relationships  
5. ✅ Post-recovery verification scripts  

**Key Lesson**:  
```python
# The fix we implemented later
class Payment(models.Model):
    order = models.ForeignKey(Order, on_delete=models.PROTECT)  # 🛡️
    # ...
```
{% include discussion.html content="Have you experienced cascade disasters? What safeguards do you use? Share below!" %}

---

## Continue Reading in Part 2
{% assign part2 = site.posts | where: "title", "The Surgical Data Recovery - How We Restored 50,382 Records" | first %}
{% if part2 %}
[**{{ part2.title }}**]({{ part2.url }})
{% else %}
*Part 2 coming soon*
{% endif %}