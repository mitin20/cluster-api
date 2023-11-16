# cluster-api

## ref: https://cluster-api.sigs.k8s.io/user/quick-start

## Install the latest release using homebrew:

brew install clusterctl

## # Enable the experimental Cluster topology feature.
export CLUSTER_TOPOLOGY=true

# Initialize the management cluster
clusterctl init --infrastructure docker

## The Docker provider does not require additional configurations for cluster templates.
However, if you require special network settings you can set the following environment variables:
# The list of service CIDR, default ["10.128.0.0/12"]
export SERVICE_CIDR=["10.96.0.0/12"]
# The list of pod CIDR, default ["192.168.0.0/16"]
export POD_CIDR=["192.168.0.0/16"]
# The service domain, default "cluster.local"
export SERVICE_DOMAIN="k8s.test"
It is also possible but not recommended to disable the per-default enabled Pod Security Standard:
export POD_SECURITY_STANDARD_ENABLED="false"

## Generating the cluster configuration

clusterctl generate cluster capi-quickstart --flavor development \
  --kubernetes-version v1.28.0 \
  --control-plane-machine-count=1 \
  --worker-machine-count=1 \
  > capi-quickstart.yaml

## Apply the workload cluster

kubectl apply -f capi-quickstart.yaml  

## Accessing the workload cluster
kubectl get cluster

You can also get an “at glance” view of the cluster and its resources by running:

clusterctl describe cluster capi-quickstart

## To verify the first control plane is up:

kubectl get kubeadmcontrolplane

## After the first control plane node is up and running, we can retrieve the workload cluster Kubeconfig.

clusterctl get kubeconfig capi-quickstart > capi-quickstart.kubeconfig
kind get kubeconfig --name capi-quickstart > capi-quickstart.kubeconfig

## Deploy a CNI solution
Calico is used here as an example.
kubectl --kubeconfig=./capi-quickstart.kubeconfig \
  apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml

kubectl --kubeconfig=./capi-quickstart.kubeconfig get nodes