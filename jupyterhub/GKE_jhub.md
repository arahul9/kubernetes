# Setting up jupyterhub on GKE

Login to your free tier google cloud platform

In the upper left hand corner you can see the options button, in that you can see all the different things google cloud can do.

### STEP 1
Select the GKE(Google Kubernetes Engine) from the options
### STEP 2
Click on create button and configure standard kubernetes cluster
### STEP 3
Leave the default values or change nodes, security, metadata as per your requirement by clicking on default pool  
### STEP 4
Click on create and wait for the cluster to be created
### STEP 5
Connect to the cloud shell
### STEP 6
Run **kubectl get nodes** to see if nodes are ready
### STEP 7
Set up helm
```
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```
### STEP 8
Generate a random hex string representing 32 bytes to use as a security token.
```
openssl rand -hex 32
```
### STEP 9
Create and start editing a file called config.yaml. 
```
nano config.yaml
```
### STEP 10
Write the following into the config.yaml file but instead of writing <RANDOM-HEX> paste the generated hex string you copied in step 1
```
proxy:
  secretToken: "<RANDOM_HEX>"
```

### STEP 11
Make Helm aware of the JupyterHub Helm chart repository so you can install the JupyterHub chart from it without having to use a long URL name.
```
helm repo add jupyterhub https://jupyterhub.github.io/helm-chart/
helm repo update
```
### STEP 12
```
RELEASE=jhub
NAMESPACE=jhub

helm upgrade --cleanup-on-fail \
  --install $RELEASE jupyterhub/jupyterhub \
  --namespace $NAMESPACE \
  --create-namespace \
  --version=0.10.6 \
  --values config.yaml
```
### STEP 13
Run **kubectl get pod --namespace jhub** to check all pods are running
### STEP 14
Run **kubectl get service --namespace jhub** to get the external ip and paste it in your web browser

Your jupyterhub is ready!
