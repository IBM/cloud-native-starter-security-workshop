

The Keycloak documentation describes pretty well how to install Keycloak in OpenShift. The difficult part was the creation of the realm which I’ve documented in my previous article Setting up Keycloak in OpenShift.

Quarkus comes with two great quides that describe how to use Keycloak in web apps and services:

* Using OpenID Connect to Protect Service Applications
* Using OpenID Connect to Protect Web Applications
Developing protected Endpoints

The service Articles provides an endpoint ‘/articles’ which only users with the role ‘user’ can access. In application.properties the Keycloak URL is defined as well as the client ID and secret.

[Related blog post](http://heidloff.net/article/security-quarkus-applications-keycloak//)