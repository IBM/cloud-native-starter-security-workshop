# Setup Istio

Normally in a production size Kubernetes cluster on IBM Cloud we would install Istio as an Add-On. There are 5 Kubernetes add-ons available: Istio, Knative, Kubernetes Terminal, Diagnostic and Debug Tools, and Static Route. 

Istio installed via the add-on is a managed service and it creates a production grade Istio instance and it requires a cluster with at least 3 worker nodes with 4 CPUs and 16 GB of memory which our lab Kubernetes cluster doesn't have.

Instead, in this lab we will install the Istio demo profile manually using `istioctl` and its standalone operator. `istioctl is available in IBM Cloud Shell, when I wrote these instructions it was at version 1.5.4 which means we will install Istio 1.5.4.

### Step 1: "Get environment": cluster name, cluster ip@

* Change to the IKS folder
```sh
  cd $ROOT_FOLDER/IKS
```
* Get the environment information
****<<<funktioniert nur im Lab (1 cluster)>>>****
```sh
  sh get-env.sh    
```

* Set the needed environment variables
```sh
  source local.env
```