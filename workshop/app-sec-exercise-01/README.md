# Setup the web-application and microservices locally

To run these optional exercises you need to ensure you have installed the following tools on you local machine and you can run them in your terminal sessions.

* [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [yarn](https://yarnpkg.com)
* [mvn](https://maven.apache.org/ref/3.6.3/maven-embedder/cli.html)
* Java 9 or higher

### Architecture 

Here is the local architecture with show that the Web-App and the two Microservices Web-API and Articles are running on the local machine in terminal sessions and Keycloak is running on Kubernetes on IBM Cloud.

![](../../images/architecture-local.png)

### Step 1: Clone the project to your local machine

```sh
git clone https://github.com/IBM/cloud-native-starter.git
cd cloud-native-starter/security
ROOT_FOLDER=$(pwd) 
```

### Step 2: Configure articles-secure

Insert your the `auth-server-url` URL of your Keycloak instance in `application.properties` file and save the file.
Therefore you use the `Keycloak URL` you got during the setup of Keycloak on IBM Cloud. 

```sh
cd $ROOT_FOLDER/articles-secure/src/main/resources
nano application.properties
```

Example:

```Java
// When running locally, uncomment the next line, add your Keycloak URL, must end on '/auth/realms/quarkus'
quarkus.oidc.auth-server-url=https://YOUR_URL/auth/realms/quarkus

quarkus.oidc.client-id=backend-service
quarkus.oidc.credentials.secret=secret

quarkus.http.port=8082
quarkus.http.cors=true

resteasy.role.based.security=true
```

### Step 3: Configure web-api-secure

Insert your the `auth-server-url` URL of your Keycloak instance in `application.properties` file and save the file.

Therefore you use the `Keycloak URL` you got during the setup of Keycloak on IBM Cloud.

```sh
cd $ROOT_FOLDER/web-api-secure/src/main/resources
nano application.properties
```

Example:

```Java
// When running locally, uncomment the next line, add your Keycloak URL, must end on '/auth/realms/quarkus'
quarkus.oidc.auth-server-url=https://YOUR_URL/auth/realms/quarkus

quarkus.oidc.client-id=backend-service
quarkus.oidc.credentials.secret=secret

quarkus.http.port=8081
quarkus.http.cors=true

resteasy.role.based.security=true
```

### Step 4: Configure web-app

Now insert `Keycloak URL`/auth in `main.js`.

```sh
cd $ROOT_FOLDER/web-app/src
nano main.js
```

Example:

```JavaScript
if (currentHostname.indexOf('localhost') > -1) {
  urls = {
    api: 'http://localhost:8081/',
    login: 'https://YOUR_URL/auth' // insert your http or https://<KeycloakURL>/auth
  }
  store.commit("setAPIAndLogin", urls);
}
```

### Step 5: Run the web-app 

Open a terminal and start the application on port 8080.

```sh
cd $ROOT_FOLDER/web-app
yarn install
yarn serve
```

### Step 6: Run the web-api-secure Microservice 

Open a second terminal and start the service on port 8081.

```sh
$ cd $ROOT_FOLDER/web-api-secure
$ mvn clean package quarkus:dev
```

### Step 7: Run the articles-secure Microservice 

Open a third terminal and start the service on port 8082.

```sh
$ cd security/articles-secure
$ mvn clean package quarkus:dev
```

### Step 8: Open the Web-App on your local browser

```
http://localhost:8080
```

Log in with the test user: alice, alice