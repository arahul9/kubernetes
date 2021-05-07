# Deploy MetalLB on Bare metal
After setting up kubernetes cluster we need to deploy metallb. MetalLB provides a network load-balancer implementation for Kubernetes clusters that do not run on a supported cloud provider, effectively allowing the usage of LoadBalancer Services within any cluster.
#### Check the ip address of the node
```
ip a s
```
#### Install sipcalc tool and analyse the network that is shown by running above command
```
sudo apt install sipcalc
which sipcalc
sipcalc x.x.x.x/20
```
Take atleast 10 ip address in the usable range and assign it to metallb
### Installation By Manifest
```
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.6/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.6/manifests/metallb.yaml
# On first install only
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```
### Verify if all the components are running
```
kubectl -n metallb-system get all
```
### Deploy a config map
create a metallb.yaml file
```
nano /tmp/metallb.yaml
```
paste the following code in the file
```
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - x.x.x.x-x.x.x.x
```
then run
```
kubectl apply -f /tmp/metallb.yaml
```

Now you will be able to see external ip for the deployments



