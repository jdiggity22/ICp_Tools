-------------------
Webb Script
-------------------

Deploy Node.js Sample Chart
Log into the master node.

### Depoy Node HELM (master)

cloudctl login -a https://yourcluster:8443 --skip-ssl-validation

```
kubectl get po. --> will show us nothing is installed
helm repo list
helm search node
helm repo add ibm-stable https://raw.githubusercontent.com/IBM/charts/master/repo/stable
helm install ibm-stable/ibm-nodejs-sample -n nodejs-demo --namespace networkdemo --tls
kubectl get deployments
kubectl get pods
kubectl get service
kubectl get pods -o wide
```



#### Ping running pod

Next ping the IP of the pod shown from the master node

```
kubectl get po -o wide
ping -c 1 <ipaddress>
```

**IPtables** **(worker)**

```
iptables-save | grep nodejssample
```

**Routing Tables(worker)**
Note the IP and node the pod is running on from the kubectl get po -o wide
Log into that node

```
ip route
```

We can see the IP from the pod associated with an interface

```
ip addr list <interface>
```


Find the interface from above

**Subnets (master)**
Back on the master node

```
ip route
```

Now we see at the top all of the /26 pod subnets and the associated node

**Network Policy and IPTables (worker)**
Back to the node running the pod
Find the interface for the pod.  <<< Need this step>>>

```
iptables -L > file
```


vi the file and search for the "Chain <interface name> …"

**Service(master)**

```
kubectl get services -o wide
Kubectl describe service <your service here>
```

Show the target port from the pod
Show the port
Show the node port

```
curl http://serviceip:port
```

From your local browser

```
http://somenodeip:<nodeport>
```


Change the ip to another node

```
Master VIP = 172.16.52.60
Worker = 172.16.52.76
```





**Bonus:**
Deploy busybox

```
kubectl run --namespace=networkdemo busybox --rm -ti --image busybox /bin/sh
```

Exec into the container
Ping a service in that namespace by service name

```
ping nodes-service
```

Ping a service in another namespace by service.namespace

```
ping cam-proxy.services
```

While you are in there cat /etc/resolv.conf







```
kubectl create -f - <<EOF
apiVersion: securityenforcement.admission.cloud.ibm.com/v1beta1
kind: ClusterImagePolicy
metadata:
  name: busybox-policy
spec:
  repositories:
  - name: docker.io/busybox
    policy: null
EOF
```