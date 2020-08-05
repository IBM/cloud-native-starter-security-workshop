# Deploy microservices to Kubernetes

You have compiled all three pieces of our sample app, articles-secure, web-api-secure, and web-app, when you ran and tested locally.

In this exercise we will use precompiled container images that we uploaded to Docker Hub.

> We cannot set the OIDC provider (keycloak) in application.properties without recompiling the code. So for this example, we specify the Quarkus OIDC property as environment variable during deployment. The environment variable is read from a config map. 

### STEP 1: Change `configmap.yaml`entry

In directory IKS, modifify configmap.yaml and edit QUARKUS_OIDC_AUTH_SERVER_URL with your keycloak URL. It must end in /auth/realms/quarkus, enclosed in "".

* Create the `QUARKUS_OIDC_AUTH_SERVER_URL` and copy the URL to insert it in the next step in the `configmap.yaml`

```sh
cd $ROOT_FOLDER/IKS
export QUARKUS_OIDC_AUTH_SERVER_URL="https://$INGRESSURL/auth/realms/quarkus"
echo $QUARKUS_OIDC_AUTH_SERVER_URL
```

* Replace the URL in the `configmap.yaml`

```sh
nano configmap.yaml
```

Example:

```sh
kind: ConfigMap
apiVersion: v1
metadata:
  name: security-url-config
data:
  QUARKUS_OIDC_AUTH_SERVER_URL: "https://harald-uebele-*****-0001.containers.appdomain.cloud/auth/realms/quarkus"
```

### STEP 2: Now deploy the 3 services:

* Deploy Articles Microservice

```sh
cd $ROOT_FOLDER/articles-secure/deployment
kubectl apply -f articles.yaml
```

* Deploy Web-API Microservice

```sh
cd $ROOT_FOLDER/web-api-secure/deployment
kubectl apply -f web-api.yaml
```

* Deploy Web-App [Vue.js](https://vuejs.org/) frontend application

```sh
cd $ROOT_FOLDER/web-app/deployment
kubectl apply -f web-app.yaml
```

### STEP 3: Adjust the Redirect URL in Keycloak:

* Try to open the Cloud-Native-Starter application in a browser. Use the `$INGRESSURL` of your cluster, which is the URL to the frontend application `Web_APP` you deployed before.

```sh
 echo https://$INGRESSURL
```

* You will see we need to configure the redirect in Keycloak

![](../../images/cns-wrong-redirect-uri.png)


* Login to Keycloak with `user: admin` and `password: admin` and ajust the Clients frontend Valid Redirect URIs: e.g. https://harald-uebele-fra05-162e406f043e20da9b0ef0731954a894-0001.eu-de.containers.appdomain.cloud/*



### STEP 4: Open the Cloud Native Starter application in your browser

In the browser open the app with e.g. https://harald-uebele-fra05-162e406f043e20da9b0ef0731954a894-0001.eu-de.containers.appdomain.cloud

Login in with user: alice, password: alice