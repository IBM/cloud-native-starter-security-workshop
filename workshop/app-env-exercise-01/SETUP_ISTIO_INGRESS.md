# Expose the Istio Ingress gateway via DNS

The following procedures are platform specific and work with a "standard classic" Kubernetes Cluster provided by the IBM Cloud Kubernetes Service (IKS) on the IBM Cloud. If you are using a VPC based or a free ("Lite") Kubernetes Cluster on the IBM Cloud or another Cloud provider or something like Minikube, the following sections will **not** work!

### Step 1: Get public IP

When we install Istio on our pre-provisioned Kubernetes Clusters on IBM Cloud, the Istio Ingress is created with a Kubernetes service of type LoadBalancer and is assigned a "floating" IP address through which it can be reached via the public Internet. You can determine this address with the following command:

```sh
kubectl get svc -n istio-system | grep istio-ingressgateway
```
Our Ingress gateway is in fact of type LoadBalancer, the second IP address of the example `149.***.131.***` is the external (public) IP address. This is the ingressIP `149.***.131.***` for the next command we will use.

Example:

```sh
istio-ingressgateway   LoadBalancer  172.21.213.52  149.***.131.***   15020:31754/TCP,...
```

### Step 2: Create a DNS subdomain

To create a DNS subdomain, a URL, for the Ingress gateway (= loadbalancer, nlb) use the following command:

```sh
ibmcloud ks nlb-dns create classic --cluster $MYCLUSTER --ip <ingressIP>
```

The new subdomain will have the form `[cluster name]-[globally unique hash]-[region]-containers.appdomain.cloud`. The output should look like this:

```sh
OK
NLB hostname was created as harald-uebele-k8s-fra05-********************-0001.eu-de.containers.appdomain.cloud
```