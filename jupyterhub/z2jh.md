# Setting up jupyterhub on bare metal
Now that we have a Kubernetes cluster and Helm setup, we can proceed by using Helm to install JupyterHub and related Kubernetes resources using a Helm chart.
We will be setting jupyterhub version 0.11.1
## Install JupyterHub
First we need to add helm repository
```
helm repo add jupyterhub https://jupyterhub.github.io/helm-chart/
helm repo update
```
Pull the values file before installing jupyterhub and redirect it to temporary yaml file
```
helm show values jupyterhub/jupyterhub > /tmp/jupyterhub.yaml
```
Generate a random hex string representing 32 bytes to use as a security token. Run this command in a terminal and copy the output
```
openssl rand -hex 32
```
paste the output in secretToken in your yaml file **nano /tmp/jupyterhub.yaml** file

Now helm install jupyterhub
```
helm install jupyterhub jupyterhub/jupyterhub --values /tmp/jupyterhub.yaml
```

Run **helm list** and check for jupyterhub

Run **kubectl get all** to check all the pods and deployments are running.

Copy and paste the external ip next to **service/proxy-public** and paste it in your browser to run jupyterhub.

Now you have basic jupyterhub running on bare metal.


