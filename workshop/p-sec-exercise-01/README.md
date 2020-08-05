# Deploy microservices to Kubernetes

You have compiled all three pieces of our sample app, articles-secure, web-api-secure, and web-app, when you ran and tested locally.

In this exercise we will use precompiled container images that we uploaded to Docker Hub.

> We cannot set the OIDC provider (keycloak) in application.properties without recompiling the code. So for this example, we specify the Quarkus OIDC property as environment variable during deployment. The environment variable is read from a config map. 

### STEP 1: Change `configmap.yaml`entry

In directory IKS, modifify configmap.yaml and edit QUARKUS_OIDC_AUTH_SERVER_URL with your keycloak URL. It must end in /auth/realms/quarkus, enclosed in "".

```sh
cd $ROOTFOLDER/IKS
nano configmap.yaml
```

### STEP 2: Now deploy the 3 services:

* Deploy Articles Microservice

```sh
cd $ROOTFOLDER/articles-secure/deployment
kubectl apply -f articles.yaml
```

* Deploy Web-API Microservice

```sh
cd $ROOTFOLDER/web-api-secure/deployment
kubectl apply -f web-api.yaml
```

* Deploy Web-App [Vue.js](https://vuejs.org/) frontend application

```sh
cd $ROOTFOLDER/web-app/deployment
kubectl apply -f web-app.yaml
```

### STEP 3: Adjust the Redirect URL in Keycloak:

Login to Keycloak as admin, Clients: frontend Valid Redirect URIs: e.g. https://harald-uebele-fra05-162e406f043e20da9b0ef0731954a894-0001.eu-de.containers.appdomain.cloud/*
Test:

In the browser open the app with e.g. https://harald-uebele-fra05-162e406f043e20da9b0ef0731954a894-0001.eu-de.containers.appdomain.cloud

Login in with user: alice, password: alice