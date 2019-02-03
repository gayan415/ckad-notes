### ConfigMap

- create a configmap
```
k create cm gayan --from-literal=fname=gayan --from-literal=lname=jayasundara -n demo
```

- get configmap value
```
k describe cm gayan -n demo
```

- create a config map using a file
```
k create cm bro --from-file=config.txt -n demo
```

- load a configmap to pod
```
k create cm option -n demo --from-literal=var5=val5
k run demo-app --image=nginx --labels=env=demo,k8s-app=app --restart=Never -n demo -o yaml --dry-run > bro.yaml
vim bro.yaml
.
..
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: demo-app
    env:
    - name: my-value
      valueFrom:
        configMapKeyRef:
          name: option
          key: var5
```

- load configmap as name
```
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: gayan
    resources: {}
    envFrom:
    - configMapRef:
        name: anotherone
  dnsPolicy: ClusterFirst
  restartPolicy: Never
```

- load configmap as a volume
```
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx-10
    resources: {}
    volumeMounts:
    - name: gayan-volume
      mountPath: /etc/lala
  dnsPolicy: ClusterFirst
  restartPolicy: Never
  volumes:
  - name: gayan-volume
    configMap:
      name: cmvolume
```

### secret

- create a secret
```
# from cli
k create secret generic mysecret --from-literal=apikey=12345 -n demo
# from a file
# echo uvoice > code
k create secret generic brocode --from-file=code
```

- get a secret
```
k get secret mysecret -n demo -o yaml --export
echo 'MTIzNDU=' | base64 -D # result 12345
```

- load a secret from Ref
```
k run nginx --image=nginx --restart=Never -o yaml --dry-run > secret.yaml
vim secret.yaml

spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    env:
    - name: APIKEY #pod env secret value
      valueFrom:
        secretKeyRef:
          name: mysecret # secret name
          key: apikey # secret value key
  dnsPolicy: ClusterFirst

k create -f secret.yaml -n demo

k exec -it nginx -- env | grep APIKEY
```

- load secret value from a volume
```
k create secret generic mysecret2 --from-file=username -n demo

.
..
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    voumeMounts:
    - name: foo # volume name
      mountPath: /etc/foo
  dnsPolicy: ClusterFirst
  restartPolicy: Never
  volumes:
  - name: foo # volume name
    secret:
      secretName: mysecret2 #secret name
```

### Request and Limit
```
k run nginx --image=nginx --restart=Never --requests='cpu=100m,memory=256Mi' --limits='cpu=200m,memory=512Mi' -n demo
```

### SecurityContext

- run as user 101
```
spec:
  securityContext:
    runAsUser: 101
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
```

- capabilities "NET_ADMIN", "SYS_TIME"
```
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    securityContext:
      capabilities:
        add: ["NEY_ADMIN", "SYS_TIME"]
```

- remote the taint from a node
```
k taint nodes ip-10-210-211-192.ec2.internal colour:PreferNoSchedule-
```

### Service Accounts

- get all service account
```
k get sa --all-namespaces
```

- create a sa account
```
k create sa 'myuser' -o yaml -n demo
```

- create a pod
```
k run nginx --image=nginx --restart=Never --serviceaccount=myuser -o yaml --dry-run > sa-pod.yaml
k apply -f sa-pod.yaml -n demo
```

### Taints and Tolerations

- taints a node

```
k taint nodes node-1 key=value:taint-effect
# taint-effect = NoSchedule | PreferNoSchedule | NoExecute
# which means
k taine nodes ip-10-210-211-192.ec2.internal color=blue:PreferNoSchedule
```

- toleration a pods
```
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
  dnsPolicy: ClusterFirst
```

- label a node
```
k label nodes <node-name> <label-key>=<label-value>
k label nodes node01 colour:blue
```

### nodeSelector, Affinity and anti-affinity

- label a pod
```
  containers:
  - args:
    - /bin/sh
    - -c
    - echo hello Gayan;sleep 3600
    image: busybox
    name: multi-con
  - name: next-gen-app
    images: busybox
  nodeSelector:
    colur: blue
```


- set affinity to deployment

```
    spec:
      containers:
      - image: nginx
        name: blue
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                - key: color
                  operator: In
                  values:
                    - blue
```
