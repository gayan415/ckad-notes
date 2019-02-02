 - create an alias
 ```
 alias k=kubectl
 ```

 - create _demo_ namespace
 ```
 k create namespace demo
 ```

 - create a nginx pod
 ```
 k run nginx --image=nginx --restart=Never --port=80 --labels=k8s-app=demo,env=demo -o yaml --dry-run > nginx.yaml
 cat nginx.yaml
 k create -f nginx.yaml -n demo
 k get po -n demo
 ```

- get pod's yaml without cluster info
```
k get po nginx -n demo -o yaml --export
```

- change image
```
k set image pod/nginx nginx=nginx:1.7.1 -n demo
k logs nginx -n demo
```

- connect to pod
```
k exec -it nginx -n demo /bin/sh
```

- echos 'hello Gayan'
```
k run busybox --image=busybox --restart=Never --rm -it -- echo 'hello Gayan'
or
k run busybox --image=busybox --restart=Never --rm -it -- /bin/sh -c 'echo hello Gayan'
```

- set env
```
k run nginx-demo --image=nginx --restart=Never --env=PASSWORD=pass123 --labels=k8s-app=nginx-demo -n demo
k exec -it nginx-demo -n demo -- env
k set image pod/nginx-gayan nginx-gayan=nginx:1.7.1 -n demo
k get pods nginx-gayan -n demo -o yaml | grep image
```

- get all pods
```
k get po --all-namespaces
```

- get all resources
```
# default/set namespace
k get all 
or
# all namespaces
k get all --all-namespaces
```

