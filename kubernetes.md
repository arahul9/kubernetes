# Setting up Kubernetes cluster using kubeadm

### Follow the instructions to setup Kubernetes cluster on Bare metal(Ubuntu 20.04LTS)

## Requirements
1. Master node with atleast 2G RAM and 2CPU's
2. Worker node with atleast 1G RAM AND 1CPU (Can add more worker nodes based on the requirement)

## Run the following commands on both master and worker
All the commands below are to be performed as root user unless otherwise specified
```
sudo su
```
#### STEP 1
Disabling the firewall

```
ufw disable
```
#### STEP 2
Disable swap
```
swapoff -a
sed -i '/swap/d' /etc/fstab
```
#### STEP 3
Update sysctl settings for Kubernetes networking
```
cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```
#### STEP 4
Install docker engine
```
apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
apt update
apt install -y docker-ce=5:19.03.10~3-0~ubuntu-focal containerd.io
 ```
### Kubernetes Setup
#### STEP 5
Add Apt repository
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
```
#### STEP 6
Install Kubernetes components
```
apt update
apt install -y kubeadm=1.20.5-00 kubelet=1.20.5-00 kubectl=1.20.5-00
```
## Only on Master node
#### STEP 7
Update the below command with the ip address of master
```
kubeadm init --apiserver-advertise-address=x.x.x.x --pod-network-cidr=192.168.0.0/16  --ignore-preflight-errors=all
````

#### STEP 8
Run the following commands exit the root to run kubectl commands as non-root user
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

#### STEP 9
Deploy Calico network as root user and exit
```
kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml
```

#### STEP 10

## On worker node join the cluster
Use the output from __kubeadm token create__ command in previous step from the master server and run here


#### STEP 11

## Verify the cluster in master node if the node is joined 
```
kubectl get nodes
```

Your Kubernetes cluster is ready!
