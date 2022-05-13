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

TODO: update the example to use `quay.io/openshift` once the images are mirrored there. `registry.ci.openshift.org` is not public.
