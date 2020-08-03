# Expose the Istio Ingress gateway via DNS

The following procedures are platform specific and work with a "standard classic" Kubernetes Cluster provided by the IBM Cloud Kubernetes Service (IKS) on the IBM Cloud. If you are using a VPC based or a free ("Lite") Kubernetes Cluster on the IBM Cloud or another Cloud provider or something like Minikube, the following sections will **not** work!

### Step 1: Get public IP

When we install Istio on our pre-provisioned Kubernetes Clusters on IBM Cloud, the Istio Ingress is created with a Kubernetes service of type LoadBalancer and is assigned a "floating" IP address through which it can be reached via the public Internet. You can determine this address with the following command:

```sh
kubectl get svc -n istio-system | grep istio-ingressgateway
```

The output should look like this:

```sh
istio-ingressgateway   LoadBalancer  172.21.213.52  149.***.131.***   15020:31754/TCP,...
```