---
title: Platform Backup
description: 
published: true
date: 2020-09-07T16:09:39.537Z
tags: 
editor: markdown
---

## Overview
While most of the applications deployed have an established backup process I'd like to centralize this and build out a single job responsbile for managing backups.

A docker container seems my best approach (this could either be invoked in the cluster, or run externally. 

## Requirements
Things I'm already comfortable with:
* The backup of this wiki's content (replicates any changes to github). 
* Docker images (all hosted on docker hub).
* Secrets - I have a manual process for this, but it is secure for my purposes and will remain as-is for now. 

I need to ensure I back up (and store encrypted):
* Platform data and metadata
  * Etcd database
  * Master Node certificates and configuration
* Databases in a restorable format - preferably using the database client tools:
  * MariaDB (contains metadata for the wiki I'd rather not have to re-configure, and my local git repo).
  * PostgreSQL (contains Jira).
  * Stateful file data:
  	* Jira uploads.
    * Git repos.
    
## Steps
1. Build a docker image with the necessary clients to establish this backup. Namely: [etcd](https://etcd.io/docs/v3.4.0/op-guide/recovery/), [postgresql-client](https://www.postgresql.org/docs/current/backup.html), [mariadb-client](https://mariadb.com/kb/en/backup-and-restore-overview/) and we'll need a way to mount NFS stores as well. 
2. Manually prove this out (step-by-step establish a backup of each item), then encrypt and save the output. I'm going to store this content on my google drive, as I already pay for business grade cloud storage there.
3. Automate the process (inject credentiials via environment variables and see if we can set this thing to auto-run).

## Open Questions
1. Stateful backup - I am likely going to need to pause my statefulsets somehow in order to back up their data (keeping the DB and platform in sync appropriately).
2. Etcd requires backup to occur on an etcd member. This may either require backup to run on the master (possible via directives on a yaml file) or the pod itself will need to be able to join the etcd cluster for the purpose of backing it up (the first seems easier for now) - as  a test run we'll use the manual copy of the master database via ssh.
