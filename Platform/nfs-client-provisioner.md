---
title: NFS-Client-Provisioner
description: Notes on Baremetal K8s Dynamic Volume storage with NFS
published: true
date: 2020-04-25T12:56:52.473Z
tags: 
---

# Dynamic Volumes with NFS
The default Helm install of NFS Client Provisioner establishes a role with access to the provided namespace only. However the storage class is registered globally.

While I had initially opted to leverage a single namespace, I'm increasingly finding holes in this approach. Namespaces provide a really clean way of segmenting from a security standpoint, but also provide a really nice way of keeping things uncluttered as I work on all kinds of rubbish in this platform. 

The tipping point was attempting to install prometheus as a logging platform, which I absolutely want to exist separately to any applications or database namespaces.

I would prefer to have a single provisioner with the necessary access to allocate volumes across the platform. An alternative would be to provision a unique storage class per namespace with the overhead of one NFS-Client-provisioner in each.

## ClusterRoles vs Roles
Cluster roles (as the name suggests) add cluster-wide permissions. I took a first pass at this with little success below.

Plaintext overview:
```
Create a role with cluster wide permissions necessary to manage storage

---

Now assign this role to the existing nfs-client-provisioner service account. 
```


Yaml File:

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  # "namespace" omitted since ClusterRoles are not namespaced
  name: cluster-leader-locking-nfs-provisioner-nfs-client-provisioner
rules:
- apiGroups:
  - ""
  resources:
  - endpoints
  verbs:
  - get
  - list
  - watch
  - create
  - update
  - patch

---
apiVersion: rbac.authorization.k8s.io/v1
# This cluster role binding allows cluster level access for the NFS-Client-Provisioner
kind: ClusterRoleBinding
metadata:
  name: nfs-provisioner-global-rolebinding
subjects:
- kind: ServiceAccount
  name: svcfns
  namespace: data-db

roleRef:
  kind: ClusterRole
  name: cluster-leader-locking-nfs-provisioner-nfs-client-provisioner
  apiGroup: rbac.authorization.k8s.io
```

Where I appear to have gone wrong is in missing some of the additional resources the role needs access to in order to function accross the cluster.

## Implementation

The cluster role below from a [similar provisioner](https://github.com/kubernetes-incubator/external-storage/issues/953) appears to be a good starting point, so I'll be aiming to reproduce this and overlay this on the helm deployment for now. If that works I'll look into options for either building this into a custom helm chart or submitting a pull request on the main repository.

Not sure I'm confident enough with this stuff to make that attempt just yet.

```
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: efs-provisioner-runner
rules:
  - apiGroups: [""]
    resources: ["persistentvolumes"]
    verbs: ["get", "list", "watch", "create", "delete"]
  - apiGroups: [""]
    resources: ["persistentvolumeclaims"]
    verbs: ["get", "list", "watch", "update"]
  - apiGroups: ["storage.k8s.io"]
    resources: ["storageclasses"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["events"]
    verbs: ["list", "watch", "create", "update", "patch"]
  - apiGroups: [""]
    resources: ["endpoints"]
    verbs: ["get", "list", "watch", "create", "update", "patch"]
```

## Patching the helm chart?
