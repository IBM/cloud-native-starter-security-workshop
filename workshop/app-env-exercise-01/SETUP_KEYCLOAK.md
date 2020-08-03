# Setup Keycloak

More details about the installation basics of Keycloak you get here [Keycloak - Guide - Keycloak on Kubernetes](https://www.keycloak.org/getting-started/getting-started-kube)

We have Istio installed and will is using the Istio Ingress to access Keycloak externally. The original `keycloak.yaml` is modified, here we removed `NodePort`. 

### Step 1: Deploy Keycloak

```sh
kubectl apply -f keycloak.yaml
```

### Step 2: Wait until the Keycloak Pod is started

```sh
kubectl get pods
```

### Step 3: Access Keycloak


Get the Keycloak URL and open this URL in your browser:

```sh
echo "https://"$INGRESSURL"/auth"
```

### Step 4: Logon to Keycloak and configure

Click on 'Administration Console'. Login In with username 'admin' and password 'admin'.

Configuration:

Move your mouse pointer over 'Master' in the upper left corner and click the blue 'Add realm' button.

DRAFT Wir müssen uns überlegen, wo die JSON file herkommt, im Zweifel aus der Cloud Shell runterladen oder per wget aus unserem Github Repo ... /DRAFT

In the 'Add realm' dialog click 'Select file', open the 'quarkus-realm.json' file, then click 'Create'.

You should see a message pop up: "Success! Realm created"

Try to create an access token, this requires the $INGRESSURL environment variable to be set:

curl -sk --data "username=alice&password=alice&grant_type=password&client_id=frontend" \
        https://$INGRESSURL/auth/realms/quarkus/protocol/openid-connect/token  \
        | jq ".access_token" | sed 's|"||g'