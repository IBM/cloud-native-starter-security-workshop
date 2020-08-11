# Expose the Istio Ingress gateway via DNS

The following procedures are platform specific and work with a **"standard classic"** Kubernetes Cluster provided by the IBM Cloud Kubernetes Service (IKS) on the IBM Cloud. 

>If you are using a VPC based or a free ("Lite") Kubernetes Cluster on the IBM Cloud or another Cloud provider or something like Minikube, the following sections will **not** work!

### Automated setup

### Step 1: Execute following script

```sh
  cd $ROOT_FOLDER
  sh IKS/istio-setup-ingress-gateway.sh
```

Example output:

```sh
...
OK
NLB hostname was created as tsuedbro-security-works-162e406f043e20da9b0ef0731954a894-0002.us-south.containers.appdomain.cloud
------------------------------------------------------------------------
Ingress-URL: harald-uebele-k8s-fra05-***-0001.us-south.containers.appdomain.cloud
Cluster Name: harald-uebele
...
```

### Manual setup

The following steps showing a the manual steps of the automated setup.

### Step 1: Get public IP

When we install Istio on our pre-provisioned Kubernetes Clusters on IBM Cloud, the Istio Ingress is created with a Kubernetes service of type LoadBalancer and is assigned a "floating" IP address through which it can be reached via the public Internet. You can determine this address with the following command:

```sh
cd $ROOT_FOLDER/IKS
kubectl get svc -n istio-system | grep istio-ingressgateway
```
Our Ingress gateway is in fact of type LoadBalancer, the second IP address of the example `149.***.131.***` is the external (public) IP address.  We will use ingressIP `149.***.131.***`  in one of the next commands.

Example:

```sh
istio-ingressgateway   LoadBalancer  172.21.213.52  149.***.131.***   15020:31754/TCP,...
```

### Step 2: Create a DNS subdomain

To create a DNS subdomain, a URL, for the Ingress gateway (= loadbalancer, nlb) for the next commanduse the following command:

```sh
echo $MYCLUSTER
ibmcloud ks nlb-dns create classic --cluster $MYCLUSTER --ip <ingressIP>
```

_Note:_ Remember the your "ingressIP"=`149.***.131.***`

The new subdomain will have the form `[cluster name]-[globally unique hash]-[region]-containers.appdomain.cloud`. The output should look like this:

```sh
OK
NLB hostname was created as harald-uebele-k8s-fra05-********************-0001.eu-de.containers.appdomain.cloud
```

### Step 3: Save Ingress URL

To reuse the Ingress URL later we create a environment variable  `INGRESSURL.

```sh
export INGRESSURL=harald-uebele-k8s-fra05-********************-0001.eu-de.containers.appdomain.cloud
```