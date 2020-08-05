 # Secure ingress with TLS (HTTPS)

Dur
Question 1: Why can we access our application with TLS (https://...) ?

Answer: We prepared this in Exercise 1, Step 3.

We let IBM Cloud create a DNS entry and Let's Encrypt certificate

We added this certificate to the Istio Ingress

We added the DNS name (host) to the Istio Ingress Gateway definition

We added it also to the VirtualService definition that configures the Gateway and here is our secret, look at IKS/istio-ingress-tls.yaml:

The Gateway definition specifies HTTPS only and points to the location of the TLS certificates.

The VirtualService definition specifies 3 rules:

If call the DNS entry / Ingress URL with '/auth' it will direct to keycloak.
With '/articles' it will direct to the web-api
Without an path it directs to the web-app itself.
Question 2: We use https in the browser but everything behind the Istio Ingress is http only, unencrypted?

Answer: That is the beauty of Istio! Yes, we make our requests via http which is most obvious with the web-app that is called on port 80.

But Istio injects an Envy proxy into every pod in the default namespace automatically. We defined this in Exercise 1. (?????WIRKLICH?????)

There is also an Envoy proxy in the Istio Ingress pod. Communication between the Envoys is always encrypted, Istio uses mTLS. And all our requests flow through the proxies so even if the communication between e.g. web-api and articles is using http, the communication between the web-api pod and the articles pod is secure.

Question 3: Is this safe?

Answer: No, at least not not totally. By default, after installation, Istio uses mTLS in PERMISSIVE mode. This allows to test and gradually secure your microservices mesh.

Continue with the next section to see how to change that.