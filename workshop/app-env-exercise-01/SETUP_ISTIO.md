# Setup Istio

Normally in a production size Kubernetes cluster on IBM Cloud we would install Istio as an Add-On. There are 5 Kubernetes add-ons available: Istio, Knative, Kubernetes Terminal, Diagnostic and Debug Tools, and Static Route.

Istio installed via the add-on is a managed service. It creates a production grade Istio instance and it requires a cluster with at least 3 worker nodes with 4 CPUs and 16 GB of memory which our lab Kubernetes cluster doesn't have.

Instead, in this lab we will install the Istio demo profile ourselves using `istioctl` and the Istio standalone operator. `istioctl` is available in IBM Cloud Shell, when we wrote these instructions it was at version 1.5.4 which means we will install Istio 1.5.4.

### Automated setup

#### Step 1: Execute following script

```sh
  cd $ROOT_FOLDER
  bash $ROOT_FOLDER/IKS/istio-setup.sh
```

Example output:

```sh
...
NAME                                    READY   STATUS    RESTARTS   AGE
grafana-5cc7f86765-bx7cg                1/1     Running   0          7d21h
istio-egressgateway-5c8f9897f7-bgvsb    1/1     Running   0          7d21h
istio-ingressgateway-65dd885d75-mg7dh   1/1     Running   0          7d18h
istio-tracing-8584b4d7f9-jnf2s          1/1     Running   0          7d21h
istiod-7d6dff85dd-nb7v6                 1/1     Running   0          7d21h
kiali-696bb665-wp2kv                    1/1     Running   0          7d21h
prometheus-564768879c-x5q7c             2/2     Running   0          7d21h

------------------------------------------------------------------------
Check grafana
Status: Running
2020-08-11 13:44:30 Status: grafana is Ready
------------------------------------------------------------------------

------------------------------------------------------------------------
Check istiod
Status: Running
2020-08-11 13:44:31 Status: istiod is Ready
------------------------------------------------------------------------

------------------------------------------------------------------------
Check prometheus
Status: Running
2020-08-11 13:44:32 Status: prometheus is Ready
------------------------------------------------------------------------

------------------------------------------------------------------------
Check istio-egressgateway
Status: Running
2020-08-11 13:44:32 Status: istio-egressgateway is Ready
------------------------------------------------------------------------

------------------------------------------------------------------------
Check istio-ingressgateway
Status: Running
2020-08-11 13:44:33 Status: istio-ingressgateway is Ready
------------------------------------------------------------------------

------------------------------------------------------------------------
Check istio-tracing
Status: Running
2020-08-11 13:44:34 Status: istio-tracing is Ready
------------------------------------------------------------------------
------------------------------------------------------------------------
Replace telemetry config to expose nodeports
------------------------------------------------------------------------
service "grafana" deleted
service "kiali" deleted
service "prometheus" deleted
service "jaeger-query" deleted
------------------------------------------------------------------------
Label namespace 'default' for auto injection
...

```

### FYI: Manual setup

The following steps show the manual steps of the automated setup. This is just for your information, you don't need to run them!

#### Step 1: Setup Istio with an operator

The following commands do install the Istio operator, create a namespace for the Istio backplane, and start to installation of the Istio backplane.

* Operator

```sh
istioctl operator init
```

* Namespace

```sh
kubectl create ns istio-system
```

* Istio deployment

```sh
kubectl apply -f istio.yaml
```

* Label 'default' namespace for Istio pod auto-injection

```sh
kubectl label namespace default istio-injection=enabled
```

#### Step 2: Check the status of Istio deployment

```sh
kubectl get pod -n istio-system
```

When deployment is completed the result should look like this:

```sh
NAME                                    READY   STATUS    RESTARTS   AGE
 grafana-5cc7f86765-65fc6                1/1     Running   0          3m28s
 istio-egressgateway-5c8f9897f7-s8tfq    1/1     Running   0          3m32s
 istio-ingressgateway-65dd885d75-vrcg8   1/1     Running   0          3m29s
 istio-tracing-8584b4d7f9-7krd2          1/1     Running   0          3m13s
 istiod-7d6dff85dd-29mjb                 1/1     Running   0          3m29s
 kiali-696bb665-8rrhr                    1/1     Running   0          3m12s
 prometheus-564768879c-2r87j             2/2     Running   0          3m12s
```
