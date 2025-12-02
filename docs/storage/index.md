---
tags:
  - storage
  - volume
  - values.yaml
---
<!-- markdownlint-disable code-block-style -->
# Getting started with durable storage

App storage generally falls into one of two categories:

- Ephemeral or *stateless*
- Persistent, or *stateful*

If your app's main concern is transactional processing, it is likely a good candidate for simple, ephemeral storage. If your app needs to reload data to start, or makes use of a database, it is likely better to use persistent storage.

## Ephemeral storage

Storage that is configured as an `emptyDir` creates a temporary volume that the pod can use.

!!! warning

    - emptyDir storage is lost if the pod restarts

Similar to secrets, we configure storage in the Helm chart `values.yaml` file.

``` yaml
...
frontend:
...
  # -- volumeMounts for the frontend container that also create corresponding `emptyDir` volumes in the pod.
  # @default -- `[{name: tmp, mountPath: /tmp}]`
  emptyDirMounts:
    - name: tmp
      mountPath: /tmp
...
```

This generates a resource manifest with a `volumeMount` configuration:

``` yaml
...
apiVersion: apps/v1
kind: Deployment
...
spec:
  template:
    spec:
      containers:
        - volumeMounts:
            - name: "tmp"
              mountPath: "/tmp"
...
      volumes:
        - name: "tmp"
          emptyDir: {}
...
```

## Persistent storage

Apps that need dedicated storage - either to avoid a loss of data when a pod restarts, or because the types of files used by the container are large binaries - can configure **persistent volumes**. Persistent volumes are configured in the Helm chart `values.yaml` file, and volumes are provisioned by the Longhorn storage operator.

``` yaml
...
frontend:
...
  # Configuration for persistent volume claims
  # @default -- `[]`
  storageMounts:
    - name: api-data-storage
      size: 10Gi
      # -- Location where the PVC will be mounted.
      mountPath: "/app/data"
      # -- Name of the storage class to use. If null it will use the
      # configured default Storage Class.
      storageClass: null
      # -- Access Mode of the storage device being used for the PVC
      accessMode: ReadWriteOnce
...
```

This generates a resource manifest with a `volumeMount` configuration:

``` yaml
...
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      containers:
        - volumeMounts:
            - name: api-data-storage
              mountPath: /app/data
...
      volumes:
        - name: api-data-storage
          persistentVolumeClaim:
            claimName: api-data-storage
...
```
