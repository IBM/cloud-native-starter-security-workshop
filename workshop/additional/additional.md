# Additional Information

Here you find additional help, something went wrong or known issues.

### In case your `Cloud Shell session was closed

#### Step 1: Go back to the open browser tab the open Kubernetes Cluster in the IBM Cloud web console. 

Now select `Access` on the left-hand side, here you see all steps to access your Kubernetes Cluster in a terminal session. You can easily copy and paste the commands. We will use these commands later to access the Kubernetes cluster in the IBM Cloud Shell.

![](../images/cluster-access-commands.png)

#### Step 2: Setup needed variable you maybe need in your lab

* `ROOT_FOLDER` of your project

```sh
git clone https://github.com/IBM/cloud-native-starter.git
cd cloud-native-starter/security
ROOT_FOLDER=$(pwd)
```

* `MYCLUSTER` your cluster name

```sh
export MYCLUSTER=YOUR-CLUSTER
```

* `INGRESSGATEWAYIP` needed to create a `DNS`

```sh
export INGRESSGATEWAYIP=$(kubectl get svc -n istio-system | grep 'istio-ingressgateway' |  awk '{print $4}')
echo $INGRESSGATEWAYIP
```

* `INGRESSSECRET` we use for Istio Ingress Gateway configuration

```sh
export INGRESSSECRET=export INGRESSSECRET=$(ibmcloud ks nlb-dns ls --cluster $MYCLUSTER | grep '0001' | awk '{print $5}')
echo $INGRESSSECRET
```

* `INGRESSURL` we use to access for example the `Cloud Native Starter` application and `Keycloak`

```sh
export INGRESSURL=$(ibmcloud ks nlb-dns ls --cluster $MYCLUSTER | awk '/-0001./ {print $1}')
echo $INGRESSURL
```

