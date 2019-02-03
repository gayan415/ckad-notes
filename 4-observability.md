### Liveness and readiness

- pod liveness 
```
k run nginx --image=nginx --restart=Never -o yaml --dry-run > liveness.yaml
vim liveness.yaml
# and add liveness
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    livenessProbe:
      initialDelaySeconds: 5 # wait 5 seconds before perform liveness
      periodSeconds: 10 # liveness check should perform every 10 seconds
      exec:
        command:
          - ls
```

- pod readiness
```
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    ports:
    - containerPort: 80
    resources: {}
    readinessProbe:
      httpGet:
        path: /
        port: 80
```
or exec check
```
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

### logs
```
k logs nginx -n demo -f
# get events - use grep to get errors
k get events
# describe may also useful
# k describe po nginx -n demo
```

### Node cpu and memory - heapster mush run
```
k top nodes
```

