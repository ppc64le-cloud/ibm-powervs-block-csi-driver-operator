# IBM PowerVS Block CSI Driver Operator

An operator to deploy the [IBM PowerVS Block CSI Driver](https://github.com/openshift/ibm-powervs-block-csi-driver) in OKD.

This operator is installed by OLM.

# Quick start

To build and run the operator locally:

```shell
# Create only the resources the operator needs to run via CLI
oc apply -f - <<EOF
apiVersion: operator.openshift.io/v1
kind: ClusterCSIDriver
metadata:
    name: powervs.csi.ibm.com
spec:
  logLevel: Normal
  managementState: Managed
  operatorLogLevel: Trace
EOF

# Build the operator
make

# Set the environment variables
export DRIVER_IMAGE=gcr.io/k8s-staging-cloud-provider-ibm/ibm-powervs-block-csi-driver:v0.1.0-alpha.3
export PROVISIONER_IMAGE=k8s.gcr.io/sig-storage/csi-provisioner:v3.1.0
export ATTACHER_IMAGE=k8s.gcr.io/sig-storage/csi-attacher:v3.4.0
export RESIZER_IMAGE=k8s.gcr.io/sig-storage/csi-resizer:v1.4.0
export NODE_DRIVER_REGISTRAR_IMAGE=k8s.gcr.io/sig-storage/csi-node-driver-registrar:v2.5.0
export LIVENESS_PROBE_IMAGE=k8s.gcr.io/sig-storage/livenessprobe:v2.6.0
export KUBE_RBAC_PROXY_IMAGE=quay.io/brancz/kube-rbac-proxy:v0.12.0

# Run the operator via CLI
./ibm-powervs-block-csi-driver-operator start --kubeconfig $KUBECONFIG --namespace openshift-cluster-csi-drivers
```

# OLM

To build an bundle + index images, use `hack/create-bundle`.

```shell
cd hack
./create-bundle registry.ci.openshift.org/ocp/4.9:ibm-powervs-block-csi-driver registry.ci.openshift.org/ocp/4.9:ibm-powervs-block-csi-driver-operator quay.io/<my-repo>/efs-bundle quay.io/<my-repo>/efs-index
```

At the end it will print a command that creates `Subscription` for the newly created index image.
Subscription command would look like below:
```
oc apply -f - <<EOF
apiVersion: operators.coreos.com/v1alpha1
kind: CatalogSource
metadata:
  name: ibm-powervs-block
  namespace: openshift-marketplace
spec:
  sourceType: grpc
  image: quay.io/<my-repo>/efs-index
EOF
```
Add the CatalogSource to the OCP cluster.

## Installing the PowerVS Block CSI Driver Operator
**Prerequisites**
- Access to the OpenShift Container Platform web console

**Procedure**
<br>To install the PowerVS Block CSI Driver Operator from the web console:
- Log in to the web console.
- Install the PowerVS Block CSI Operator:
  - Click **Operators → OperatorHub**
  - Locate the PowerVS Block CSI Operator by typing **PowerVS Block CSI** in the filter box
  - Click the **PowerVS Block CSI Driver Operator** button
  - On the PowerVS Block CSI Driver Operator page, click **Install**
  - On the Install Operator page, ensure that:
    - **All namespaces** on the cluster (default) is selected
    - Installed Namespace is set to **openshift-cluster-csi-drivers**
  - Click **Install**
  <br>After the installation finishes, the PowerVS Block CSI Operator is listed in the **Installed Operators** section of the web console.
- Install the PowerVS Block CSI Driver:
  - Click **administration → CustomResourceDefinitions → ClusterCSIDriver**
  - On the Instances tab, click **Create ClusterCSIDriver**
  - Use the following YAML file:
    ```
    apiVersion: operator.openshift.io/v1
    kind: ClusterCSIDriver
    metadata:
        name: powervs.csi.ibm.com
    spec:
      managementState: Managed
    ```
  - Click Create
  - Wait for the following Conditions to change to a "true" status:
    - PowerVSDriverCredentialsRequestControllerAvailable
    - PowerVSDriverNodeServiceControllerAvailable
    - PowerVSDriverControllerServiceControllerAvailable
