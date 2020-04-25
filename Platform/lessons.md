---
title: Notes and Learning
description: Interesting things I've encountered along the way.
published: true
date: 2020-04-19T16:23:04.970Z
tags: 
---

# Notes and Learning 
## Statefulsets
On information I've learned about the behaviour of statefulsets - which are useful in the deployment of applications which are *not* stateless (Databases and the like).

### Applications and ini files
Configmaps are the kubernetes structure that allows the injection of configuration data into a container. Unfortunately when working with Gitea I discovered that the docker image expects a read-write ini file, which conflicts with the configmap design.

While a longer term solution for this issue would be to rebuild the gitea docker image, I've opted to allow the creation of the file and mounted it onto a persistent NFS volume. Further implications for health monitoring of this setup are discussed below.


### PVCs

*Persistent volumes and claims are... persistent* 
While this seems fairly obvious occasionally when testing this turned out to be the cause of a lot of silly mistakes for me.

What I'd missed here was that they remain *after* a kubernetes statefulset or helm deployment is removed, unless manually cleaned up. The most time consuming example of this I've hit so far was in provisioning a statefulset of MariaDB. 

I had managed to deploy a corrupt series of database files, which then caused me to spend a good 2-3 hours troubleshooting NFS configuration as every deployment seemed to exhibit the same issue. 

When I finally realized I had been inadvertently referencing the same corrupt volume the whole time I was quite relieved as a working deployment was then a matter of deleting the existing volume and allowing the system to provision a fresh new database error free. 

## Effective health monitoring

### MariaDB, startup times and an improved 
I had restarted the cluster and NFS storage in order to place the power units on a more robust surge protector board. There's enough cash in this small setup now that it would cause me some angst if it were to burn out. 

After restarting I noticed that while the back-end MariaDB instance was reporting as online and ready, the front end applications were failing. In inspecting the logs it turned out that authentication to the database was failing.

In the next half hour of puzzling through what was actually occurring I ended up getting to the root of the problem. What was missing was a good readiness and liveliness probe. These appear to be *key* elements in a stateful application.

#### Sequence of events:
1. On reboot the worker nodes restart in a matter of seconds.
2. At the same time, the NFS server takes a much longer period to start up as it performs disk checks and starts up key underlying services.
3. As a result, the MariaDB statefulset boots up without a working connection to the NFS drive. Having no existing data the application then happily performs its first run settings and creates a new MariaDB database. 
4. With the database reporting ready, kubernetes exposes the database port internally to the front end applications and reports the pod as ready.
5. The applications fail to authenticate. The newly initialized database has none of the pre-configured data they are looking for, and the user accounts do not exist.

Fixing this was as simple as rebooting the database service, but with an effective readiness probe this could be avoided. A good probe could be to connect with a unique username and password (stored as a secret against the db) to a non-default database, confirming that not only is MariaDB running but that *my* MariaDB data is ready.

If this probe failed, the system would have automatically reboot the pod, and I would not have had to spend 30 minutes trying to figure out what had gone wrong.
