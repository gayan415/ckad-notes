### Volume

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - image: busybox
    name: busybox
    resources: {}
    volumeMounts:
    - name: foo # name should be same as volume name
      mountPath: /opt #path on the pod
  dnsPolicy: ClusterFirst
  restartPolicy: Never
  volumes:
  - name: foo
    hostPath:
      # instad of path we can use emprt directory emptyDir: {}
      path: /data # directory location on host
      type: Directory # this field is optional
status: {}
```

### Persistent Volume (PV)

```
kind: PersistentVolume
apiVersion: v1
metadata:
  name: myvolume
spec:
  storageClassName: normal
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
    - ReadWriteMany
  hostPath:
    path: /etc/foo
```

### Persistent Volume Claim (PVC)

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  storageClassName: normal
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 8Gi
```

### How to use PersistentVolume on a pod

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - image: busybox
    name: busybox
    resources: {}
    volumeMounts:
    - name: bar # same as pvc claimName
      mountPath: /etc/foo
  dnsPolicy: ClusterFirst
  restartPolicy: Never
  volumes:
  - name: bar
    persistentVolumeClaim:
      claimName: my-pvc # same as PVC
status: {}
```
