# NFS Subdirectory External Provisioner
The NFS subdir external provisioner is an automatic provisioner for Kubernetes that uses your already configured NFS server, automatically creating Persistent Volumes.

##### Prerequisites
Kubernetes >=1.9
Existing NFS Share

First we need to check if we have nfs server if not we need to setup nfs-server
### Install NFS-Kernel Server in Ubuntu
The first step is to install the nfs-kernel-server package on the server.
```
sudo apt update
sudo apt install nfs-kernel-server
```
### Create an NFS Export Directory
```
sudo mkdir -p /srv/nfs/kubedata
sudo chown -R nobody:nogroup /srv/nfs/kubedata
```
### Grant NFS Share Access to Client Systems
Permissions for accessing the NFS server are defined in the /etc/exports file. 
```
sudo nano /etc/exports
```
add the following line
```
/srv/nfs/kubedata    *(rw,sync,no_subtree_check,no_root_squash,no_all_squash,insecure)
```
save it and exit
Enable and start the nfs server
```
sudo systemctl	enable --now nfs-server
sudo exportfs -rav
sudo showmount -e localhost 
```
### To verify the mount login to your Kubernetes worker nodes and check if the nodes can mount nfs volumes
```
apt install -y nfs-common 
mount -t nfs ip_address:/srv/nfs/kubedata /mnt
```
check the mount and unmount it
```
umount /mnt
```

## Install helm 
The simplest way to install Helm is to run Helm’s installer script in a terminal
```
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```
## Helm install NFS subdir external provisioner
#### First add helm repo
```
helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
```
#### Install NFS subdir external provisioner
change the ip address and give path as /srv/nfs/kubedata
```
helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner --set nfs.server=x.x.x.x --set nfs.path=/srv/nfs/kubedata
```
Run **helm list** to check if nfs-provisioner is deployed

Run **kubectl get pods** to check if nfs pod is running

Now your NFS dynamic provisioner is set!

Set the storage class as default
```
kubectl patch storageclass nfs-client -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
