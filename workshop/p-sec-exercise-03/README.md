# Istio Authorization

**This is an optional lab, run through it if time permits.**

Besides authentication using mTLS, Istio can also provide authorization services:
* End-user to workload
* Workload to workload

The 'end-user to workload' authentication we handle in our example in the application code itself, you will learn about it in the last section of our workshop (Application security with Keycloak and Quarkus).

In this exercise we will learn how to apply authorization policies to further secure communication within the service mesh, workload to workload. In our example we will use [Kubernetes Service Accounts](https://v1-16.docs.kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) to perform the authorization.

When you create a pod, if you do not specify a service account, it is automatically assigned the 'default' service account in the same namespace. You can check this for the 'articles' service:

```sh
kubectl get pod
```

Get the full name of the articles pod from the resulting list:

```sh
NAME                        READY   STATUS    RESTARTS   AGE
articles-5df77c46b4-h4jgv   2/2     Running   0          3d23h
keycloak-77cffb978-nbmjj    2/2     Running   0          3d23h
web-api-5c9698b875-c8vrt    2/2     Running   0          3d23h
web-app-79499c4b99-dv2hs    2/2     Running   0          3d23h
```

Now display the details for the pod in YAML format and search for the term 'serviceAccount':

```sh
kubectl get pod articles-5df77c46b4-h4jgv -o json | grep serviceAccount
```

Result:

```sh
"serviceAccount": "default",
"serviceAccountName": "default",
```

The articles pod indeed uses the 'default' service account.

### Step 1: Modify deployments to use service accounts

First we create 2 service accounts (sa) for our 2 services 

```sh
kubectl create sa articles
kubectl create sa web-api
```

Then we replace the deployment descriptions to use the service accounts we just created: 

```sh
kubectl replace -f $ROOT_FOLDER/articles-secure/deployment/articles-sa.yaml
kubectl replace -f $ROOT_FOLDER/web-api-secure/deployment/web-api-sa.yaml
```

This will recreate the articles and web-api pods. Check with:

```sh
kc get pod
kc get pod articles-xxxxxxxxxx-yyyyy -o json | grep serviceAccount
```
Result:

```
"serviceAccount": "articles",
"serviceAccountName": "articles"
```

If you test the application in the browser it should work exactly the same as before.

### Step 2: Authorization Policy

First we apply an incomplete authorization policy to the articles service. It looks like this:

```yaml
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: articlesaccess
spec:
  selector:
    matchLabels:
      app: articles
  action: ALLOW
```

Istio documentation specifies: *If any allow policies are applied to a workload, access to that workload is denied by default, unless explicitly allowed by the rule in the policy.*

We have an "ALLOW" policy but no rule is specified which makes it effectively a "DENY ALL" rule.

Apply with:

```sh
kubectl apply -f IKS/authorization.yaml
```

Check the application in the browser again. It may take a while for the policy to propagate to the Envoy but eventually you will see this error in the browser:

```
 Articles could not be read
 Error: Request failed with status code 500
```

Now we use a correct authorization poliy. It looks like this:

```yaml
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: articlesaccess
spec:
  selector:
    matchLabels:
      app: articles
  action: ALLOW
  rules:
  - from:
    - source:
        principals: ["cluster.local/ns/default/sa/web-api"]
    to:
    - operation:
        methods: ["GET", "POST"]    
```        

It allows 'GET' and 'POST' access to the articles service for the service account (sa) 'web-api' in namespace (ns) 'default'. 

Apply with:


```sh
kubectl apply -f authorization-w-rule.yaml
```

Check the application in the browser again. It may take a while for the policy to propagate to the Envoy but eventually you will see that the application works.

