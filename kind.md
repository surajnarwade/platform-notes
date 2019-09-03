Kind Cluster
============

* Installing Kind: 

https://kind.sigs.k8s.io/docs/user/quick-start/#installation


* Mapping ports to host machine

https://kind.sigs.k8s.io/docs/user/quick-start/#mapping-ports-to-the-host-machine

* kind config

```
kind: Cluster
apiVersion: kind.sigs.k8s.io/v1alpha3
nodes:
- role: control-plane
- role: worker
  extraPortMappings:
  - containerPort: 30143
    hostPort: 30143
networking:
  apiServerAddress: <machine-private-ip>
```

* Test service spec

```
apiVersion: v1
kind: Service
metadata:
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  externalTrafficPolicy: Cluster
  ports:
  - nodePort: 30143
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  type: NodePort
```
