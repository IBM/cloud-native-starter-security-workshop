# Overview: Setup Istio and Keycloak

We need Keycloak for authentication and authorization. And we need Istio to secure access to our services.

In the following exercises we will:

* Install Istio on the IBM Cloud Kubernetes Service (IKS).
* We will use the Istio Ingress gateway to gain access to our sample application and to Keycloak externally with a DNS entry.
* We will secure the Istio Ingress gateway with HTTPS using a certificate that is automatically generated.
* Install Keycloak within the Istio Service Mesh.
