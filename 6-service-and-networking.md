- create nginx pod and open port 80
```
k run nginx --image=nginx --restart=Never --labels=app=nginx --port=80 --expose -o yaml --dry-run > service.yaml
```

- Get ClusterIP and endpoint
```
k get svc #clusterIP/svc
k get ep # endpoint
```

- Hit the ip using temp busybox
```
k run busybox --rm -it --image=busybox --restart=Never /bin/sh
wget -O- <ClusterIP>:80
```

- Change the existing service to NodePort and hit the nginx
```
k edit svc nginx

apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
  selfLink: /api/v1/namespaces/default/services/nginx
spec:
  externalTrafficPolicy: Cluster
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  sessionAffinity: None
  type: NodePort # Edit from ClusterID to NodePort
status:
  loadBalancer: {}

# check the svc change the service type
→ k get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   172.20.0.1       <none>        443/TCP        18d
nginx        NodePort    172.20.139.142   <none>        80:31447/TCP   19m

# get the node ip 
k describe po nginx

# hit the pod
wget -O- <NODE_IP>:31447
```

- create a deployment (port 8080) and expose that using svc (port 6262)
```
# create the deployment first
k run foo --image=dgkanatsios/simpleapp --replicas=3 --labels=app=foo --port=8080 --expose -o yaml --dry-run > deployment.yaml
cat deployment.yaml
k create -f deployment.yaml

# create the svc
k expose svc foo --port=6161 --target-port=8080

k get svc foo # get clusterIP
k get ep foo # get all three ip's from replicas

# verification
k get svc foo #get clusterIP
k run busybox --image=busybox --rm -it --restart=Never -- sh
wget -O- foo:6262
wget -O- ClusterIP:6262
```

- network policy - allow pods with access=true label can access the svc
```
k run nginx --image=nginx --labels=app=nginx --replicas=2 --port=80 --expose
#create networkpolicy
vim policy.yaml

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: nginx-ingress
spec:
  podSelector:
    matchLabels:
      app: nginx # selector for the node
  ingress: # allow ingress traffic
  - from:
    - podSelector: # from pod
      matchLabels: # allow ingress traffic
        acccess: 'true' # label value should be in quote like 'true'
```
