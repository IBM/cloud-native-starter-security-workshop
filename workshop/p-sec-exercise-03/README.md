# Switch MTLS to strict

**TBD**

### STEP 1: Create a access-token 

```sh
export access_token=$(curl -d "username=alice" -d "password=alice" -d "grant_type=password" -d "client_id=frontend" https://$INGRESSURL/auth/realms/quarkus/protocol/openid-connect/token  | sed -n 's|.*"access_token":"\([^"]*\)".*|\1|p')
echo $access_token
```

### STEP 2: Get the NodePort of the Web-API Microservice

```sh
export nodeport=$(kubectl get svc web-api --ignore-not-found --output 'jsonpath={.spec.ports[*].nodePort}')
echo $nodeport
```

### STEP 3: Get a external Worker IP of the Web-API Microservice

```sh
export workerip=$(ibmcloud ks workers --cluster $MYCLUSTER | awk '/Ready/ {print $2;exit;}')
echo $workerip
```

### STEP 4: Use no TLS just `HTTP` to get the articles from the Web-API Microservice

_Note:_ REMENBER a access-token is only 60 seconds valid ;-).

```sh
curl -i http://$workerip:$nodeport/articles -H "Authorization: Bearer $access_token"
```

Example output:

```sh
HTTP/1.1 200 OK
cache-control: no-cache
content-length: 1663
content-type: application/json
x-envoy-upstream-service-time: 60
x-envoy-peer-metadata: CjgKDElOU1RBTkNF*****kaB3dlYi1hcGk=
x-envoy-peer-metadata-id: sidecar~172.30.83.82~web-api-5c9698b875-sn9ck.default~default.svc.cluster.local
date: Wed, 05 Aug 2020 14:15:57 GMT
server: istio-envoy
x-envoy-decorator-operation: web-api.default.svc.cluster.local:8081/*

[{"authorBlog":"","authorTwitter":"","title":"Blue Cloud Mirror — (Don’t) Open The Doors!","url":"https://haralduebele.blog/2019/02/17/blue-cloud-mirror-dont-open-the-doors/"},{"authorBlog":"","authorTwitter":"","title":"Recent Java Updates from IBM","url":"http://heidloff.net/article/recent-java-updates-from-ibm"},******* "title":"Three awesome TensorFlow.js Models for Visual Recognition","url":"http://heidloff.net/article/tensorflowjs-visual-recognition"},{"authorBlog":"","authorTwitter":""]
´´