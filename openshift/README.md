# OpenShift Cluster API Operator helm chart

OpenShift (OCP) Cluster API Operator helm chart is based on the k8s-sig cluster-api-operator helm [chart](https://github.com/kubernetes-sigs/cluster-api-operator/tree/main/hack/charts/cluster-api-operator).  The OpenShift helm chart uses the [redhat-registry](https://catalog.redhat.com/software/containers/explore) container images to deploy the cluster-api-operator, cluster-api and cluster-api-aws-providers.

### Prerequisites

A cert-manager Operator must be installed. Select one of the following:

1. Install the cert-manager Operator for Red Hat using these directions:
   https://docs.openshift.com/container-platform/4.17/security/cert_manager_operator/cert-manager-operator-install.html
    
2. Install the jetstack cert-manager Operator using the directions below:

   2a. Add the jetstack cert manager helm repository:
   ```
   helm repo add jetstack https://charts.jetstack.io --force-update
   helm repo update
   ```
   2b. Install the jetstack cert manager:
   ```
   helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --set installCRDs=true
   ```
## Install

1. Add the CAPI Operator helm repository.

```
helm repo add capi-operator https://raw.githubusercontent.com/openshift/cluster-api-operator/refs/heads/main/openshift
helm repo update
```

2. Create the AWS credentials environment variables.

```
export AWS_REGION=us-east-1 
export AWS_ACCESS_KEY_ID=<your-access-key>
export AWS_SECRET_ACCESS_KEY=<your-secret-access-key>
export AWS_SESSION_TOKEN=<session-token> # If you are using Multi-Factor Auth.
#MacOS Note - remove the "-w0" flag from the command below 
export AWS_B64ENCODED_CREDENTIALS=$(clusterawsadm bootstrap credentials encode-as-profile|base64 -w0)
echo $AWS_B64ENCODED_CREDENTIALS
```


3. Install the OpenShift Cluster-api-operator.

```
helm upgrade --install capi-operator capi-operator/cluster-api-operator --create-namespace -n capi-operator-system --set awsEncodedCredentials=$AWS_B64ENCODED_CREDENTIALS
```

4. Set the Red Hat OpenShift credentials at the cluster-api-aws-provider. 

Run the ```helm upgrade install``` command below after replacing the ```<set-redhat-api-credentials-token>``` value at the end of the command below with your API authentication token (https://console.redhat.com/openshift/token).  
   
```
helm upgrade --install capi-operator capi-operator/cluster-api-operator --create-namespace -n capi-operator-system --set awsEncodedCredentials=$AWS_B64ENCODED_CREDENTIALS --set ocmToken=<set-redhat-api-credentials-token>
```


## Troubleshooting
Confirm the following:
1. The cluster-api-operator was deployed.

```
oc get deploy -n capi-operator-system
NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
capi-operator-cluster-api-operator   1/1     1            1           45m
```

2. The coreProvider was deployed.

```
$ oc get coreProvider -n capi-system
NAME          INSTALLEDVERSION   READY
cluster-api   v1.8.4             True
```

3. The cluster-api deployment is running.
```
oc get deploy -n capi-system
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
capi-controller-manager   1/1     1            1           47m
```

4. The infrastructureProvider was deployed.

```
oc get infrastructureProvider -n capa-system
NAME   INSTALLEDVERSION   READY
aws    v2.6.1             True
```
5. The cluster-api-aws-provider deployment is running.

```
oc get deploy -n capa-system
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
capa-controller-manager   1/1     1            1           48m
```

## Uninstall

Uninstall the  OpenShift Cluster-api-operator chart

```
helm delete capi-operator -n capi-operator-system
```
