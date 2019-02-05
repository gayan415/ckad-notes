### pod level operations
- create nginx pods
```
k run nginx1 --image=nginx --restart=Never --labels=app=v1 -n demo
k run nginx2 --image=nginx --restart=Never --labels=app=v1 -n demo
k run nginx3 --image=nginx --restart=Never --labels=app=v1 -n demo
```

- get pods with all labels
```
k get po -n demo --show-labels
```

- override/ label on a pod
```
k label po nginx3 -n demo app=v3 --overwrite
```

- get all pods labeled as app
```
k get po -L app
```

- get nginx3 pod only
```
k get po -n demo --selector=app=v3
# or
k get po -n demo -l app=v3
```

- remove app label from all pods
```
l label po nginx1 nginx2 nginx3 -n demo app-
```

- add labels again
```
k label po nginx1 nginx2 nginx3 -n demo app=v1
```

- add annotation
```
k annotate po nginx1 -n demo env=demo
# k describe po nginx1 to see annotation
```

- remote annotation
```
k annotate po nginx1 -n demo  env-
```

- add node selector
```
spec:
  containers:
  - name: gayan
    image: busybox
  nodeSelector:
    car: tesla
```

### Deployments

- run a deployment
```
k run ngix --image=nginx --replicas=2 --labels=app=demo-pod --port=80 --requests='cpu=100m,memory=512Mi' --limits='cpu=200m,memory=720Mi' -n demo
```

- get deploy
```
k get deploy ngix -n demo -o yaml --export
```

- set image
```
k set image deploy ngix -n demo ngix=nginx:1.7.9
```

- check lrollout history
```
k rollout history deploy ngix -n demo
```

- how to undo rollout deployment
```
k rollout undo deploy ngix -n demo 
```

- set image
```
k set image deploy ngix -n demo ngix=ngix:1.91
k rollout history deploy ngix -n demo
```

- chance replicas 
```
# deployment
k scale deploy ngix -n demo --replicas=6
# scale replicaset (if you deploy rs instad of deployment)
k scale rs new-replica-set -n demo --replicas=2
```

- auto-scale during CPU
```
k autoscale deploy ngix -n demo --min=3 --max=6 --cpu-precent=60
```

- pause deployment
```
k rollout pause deploy ngix -n demo
```

- resume deployment
```
k rollout resume deply ngix -n demo
```

- delete deployment
```
k delete deploy ngix -n demo
```

### Jobs

- create a job
```
k run busybox --image=busybox --restart=OnFaliure -n demo -- /bin/sh -c 'echo hello gayan;sleep 3600'
```

- check the logs
```
k logs jobs/busybox -n demo 
# or get the logs from the pods 
k get po -n demo
k logs busybox-xxx -n demo -f
```

- set completion cout
```
k run gayan --image=busybox --restart=OnFailure -o yaml --dry-run -- /bin/sh -c 'echo gayan;sleep 3600' > job.yaml
vim job.yaml

apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  labels:
    app: demo-job
  name: gayan
spec:
  completions: 5 # adding this
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: demo-job
    spec:
      containers:
      - args:
        - /bin/sh
        - -c
        - echo gayan jayasundara;sleep 3600
        image: busybox
        name: gayan
        resources: {}
      restartPolicy: OnFailure
status: {}
```

- delete job
```
k delete job gayan -n demo
```

### CronJobs

- create a cronjob
```
k run gayan --image=busybox --restart=OnFailure --schedule='*/1 * * * *' --labels=app=gayan-app --requests='cpu=100m,memory=512Mi' --limits='cpu=200m,memory=700Mi' -n demo -o yaml --dry-run -- /bin/sh -c 'echo gayan is awesome;sleep 3600'
```

- check logs
```
k get cj -n demo
k get jobs -n demo
k logs jobs/gayan-1549222620 -n demo -f
# get the pod name and tail log
k logs gayan-xxxx -n demo -f
```

- delete the job
```
k delete cj gayan -n demo
```

