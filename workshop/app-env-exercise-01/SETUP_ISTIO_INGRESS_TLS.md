# Expose the Istio Ingress gateway via DNS with TLS enabled

In the last exercise we created a DNS entry for the Ingress controller. But access to our resources was using unsecure HTTP on port 80. In this exercise  we enable secure HTTPS access on port 443.

The procedure we will use in this exercise is documented in the IBM Cloud documentation [here](https://cloud.ibm.com/docs/containers?topic=containers-istio-mesh#istio_expose_bookinfo_tls).

There is also generic documentation about [Secure Gateways](https://istio.io/latest/docs/tasks/traffic-management/ingress/secure-ingress/) available in the Istio documentation.

The Istio Ingress gateway on the IBM Cloud is of type LoadBalancer and in the last exercise we created a `DNS subdomain` for it. This also automatically generates a `encrypt certificate for HTTPS/TLS traffic` and creates a Kubernetes secret containing this certificate. We need to pull the certificate from this secret, change its name, and create a new secret so that the Istio Ingress gateway can use it.

### Step 1: List the DNS subdomains

```sh
ibmcloud ks nlb-dns ls --cluster $MYCLUSTER
```

Example output:

```sh
Hostname                                                                                            IP(s)                       Health Monitor   SSL Cert Status   SSL Cert Secret Name                                            Secret Namespace   
harald-uebele-k8s-fra05-********************-0000.us-south.containers.appdomain.cloud   169.46.52.50,169.48.97.58   enabled          created           harald-uebele-k8s-fra05-********************-0000   default   
harald-uebele-k8s-fra05-********************-0001.us-south.containers.appdomain.cloud   169.48.97.62                None             created           harald-uebele-k8s-fra05-********************-0001   default
```

### Step 2: Save Ingress secret

You should see 2 entries, the first is for the Kubernetes Ingress that is created for you when the cluster is created. The second is the Istio Ingress subdomain you created in the last exercise. 

Copy the "SSL Cert Secret Name" (should end on -0001) and paste it into another environment variable:

```sh
export INGRESSSECRET=harald-uebele-k8s-fra05-********************-0001
```

### Step 3: Pull the secret and save it into a file 'mysecret.yaml'

```sh
kubectl get secret $INGRESSSECRET --namespace default --export -o yaml > mysecret.yaml
```

### Step 4: Edit the 'mysecret.yaml'

The secret was created in the 'default' namespace. In order to use it with Istio, we want to modify the name and place it in the 'istio-system' namespace.

Open the mysecret.yaml file in an editor nano, change the value of the secret name from `name: harald-uebele-k8s-fra05-******-0001` to `istio-ingressgateway-certs` and save the file.

```sh
nano mysecret.yaml
```

Your changed file should look similar to this example:

```yml
apiVersion: v1
data:
  tls.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk...
  tls.key: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS...
kind: Secret
metadata:
  annotations:
    ingress.cloud.ibm.com/cert-source: ibm
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"tls.crt":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FU...
  creationTimestamp: null
  name: istio-ingressgateway-certs
  selfLink: /api/v1/namespaces/default/secrets/harald-uebele-k8s-***************-0001
type: Opaque
```

### Step 5: Load and activate the secret with these commands

The last command deletes the Istio Ingress pod to force it to reload the newly created secret.

```sh
kubectl apply -f ./mysecret.yaml -n istio-system
kubectl delete pod -n istio-system -l istio=ingressgateway
```

### Step 6: Get the `$INGRESSURL` you obtained in the last exercise and copy or note the value.

```sh
echo $INGRESSURL
```

### Step 7: Edit the file `istio-ingress-tls.yaml`

Edit the file istio-ingress-tls.yaml in the istio directory. 

```sh
nano istio-ingress-tls.yaml
```

Replace the 2 occurances of wildcard "*", one in the Gateway, one in the VirtualService definition and save the file. 
Watch out for the correct indents, this is YAML!

```yml
...
 servers:
  - port:
      number: 443
      name: https
      protocol: HTTPS
    tls:
      mode: SIMPLE
      serverCertificate: /etc/istio/ingressgateway-certs/tls.crt
      privateKey: /etc/istio/ingressgateway-certs/tls.key
    hosts: "*"
...
apiVersion: networking.istio.io/v1alpha3kind: VirtualService
metadata:  name: virtualservice-ingress
spec:
  hosts:
  - "*"
  gateways:
...
```

### Step 8: Apply the change

This last step, replacing the wildcard host "*" with the correct DNS name, is not really necessary. But now you have a correct configuration that is secure.

`istio-ingress-tls.yaml` creates an Istio Gateway configuration using the TLS certificate stored in a Kubernetes secret. It also generates a VirtualService definition for this Gateway with routes to services that do not exist at the moment.

```sh
kubectl apply -f istio-ingress-tls.yaml
```

_Note:_ There is a blog on the Istio page that describes how to [Direct encrypted traffic from IBM Cloud Kubernetes Service Ingress to Istio Ingress Gateway](https://istio.io/latest/blog/2020/alb-ingress-gateway-iks/). With this scenario you can have non-Istio secured services communicate with services secured by Istio, e.g. while you are migrating your application into Istio.

This blog contains an important piece of information regarding the Let's Encrypt certificates used:

> The certificates provided by IKS expire every 90 days and are automatically renewed by IKS 37 days before they expire. You will have to recreate the secrets by rerunning the instructions of this section every time the secrets provided by IKS are updated. You may want to use scripts or operators to automate this and keep the secrets in sync.


