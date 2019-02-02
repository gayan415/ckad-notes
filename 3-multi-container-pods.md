- create single pod menifest file first
```
k run multi-con --image=busybox --restart=Never -o yaml --dry-run -- /bin/sh -c 'echo hello Gayan;sleep3600' > pod.yaml
```

- add the second container
```
spec:
  containers:
  - args:
    - /bin/sh
    - -c
    - echo hello Gayan;sleep 3600
    image: busybox
    imagePullPolicy: IfNotPresent
    name: multi-con
    resources: {}
  - name: next-gen-app
    images: busybox
    args:
    - /bin/sh
    - -c
    - echo nice try;sleep 600
  dnsPolicy: ClusterFirst
  restartPolicy: Never
```

- apply/create
```
kubectl create -f pod.yaml -n demo
```

