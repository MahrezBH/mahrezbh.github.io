---
title: "Your Self-Hosted GitLab is Eating Your Disk: Mastering registry-garbage-collect"
date: 2026-01-13
categories: [DevOps, GitLab]
tags: [infrastructure, self-hosted, ci-cd]
---

If you are running a self-hosted GitLab instance, your Container Registry is quietly operating as a black hole for your disk space. 

There is a fundamental misunderstanding about how GitLab handles Docker images. When you go into the GitLab UI and delete an image tag, **GitLab does not delete the actual data**. It simply untags it. The heavy layers, the gigabytes of Ubuntu base images and node_modules, stay perfectly intact on your server, spinning up your storage costs until your instance crashes with a `No space left on device` error.

Here is the blueprint for how you should be reclaiming your GitLab disk space.

## The 3-Phase Disk Reclamation Strategy

### Phase 1: Bypassing "The UI Lie"
When a pipeline runs and pushes `my-api:latest`, the previous `latest` image loses its tag but keeps its data. Clicking "Delete" in the GitLab UI does the exact same thing. 

To actually purge this orphaned data from your server, you have to SSH into your GitLab instance and run the built-in garbage collection utility: `registry-garbage-collect`.



---

### Phase 2: The `-m` Flag Magic
By default, the garbage collector only looks for unreferenced *layers*. But if an untagged manifest still references those layers, the GC skips them. You need to explicitly tell GitLab to aggressively hunt down untagged manifests as well.

**The Implementation:**

```bash
# 1. First, do a dry run to see how many GBs you are wasting
sudo gitlab-ctl registry-garbage-collect --dry-run

# 2. Run the actual GC WITH the -m flag (delete untagged manifests)
sudo gitlab-ctl registry-garbage-collect -m

```

{% include warning.html content="**Critical**: If a CI/CD pipeline pushes an image *while* you are running garbage collection, you will corrupt your registry state. You must protect the registry before running the purge." %}

---

### Phase 3: The Zero-Downtime Tactic

The "junior" way to protect the registry during GC is to shut it down entirely (`sudo gitlab-ctl stop registry`). But if you have an active team, taking the registry offline breaks everyone's pipelines.

The senior approach is to flip the registry into **read-only mode**. Pipelines can still pull images to run deployments, but pushes are temporarily blocked while the disk cleans itself.

**The Workflow:**

1. Edit your `/etc/gitlab/gitlab.rb` file:

```ruby
registry['storage'] = {
  'maintenance' => {
    'readonly' => {
      'enabled' => true
    }
  }
}
```


2. Apply the change: `sudo gitlab-ctl reconfigure`
3. Run the purge: `sudo gitlab-ctl registry-garbage-collect -m`
4. Revert the `gitlab.rb` file (set `enabled` to `false`) and run `sudo gitlab-ctl reconfigure` again.

---

## Critical Lessons

Self-hosting GitLab gives you incredible control, but it means you are responsible for the janitorial work.

### My Maintenance Protocols:

1. **Never trust the UI:** The GitLab UI manages *metadata*. The CLI manages the *disk*.
2. **Always use `-m`:** Running garbage collection without the `-m` flag usually leaves 80% of the wasted space untouched.
3. **Automate the cleanup:** Don't do this manually. Write a script to toggle read-only mode, run the GC, and revert it, then stick it on a weekly midnight cron job.
