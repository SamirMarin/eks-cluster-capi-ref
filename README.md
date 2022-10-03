# eks-cluster-capi-ref
reference for deploying eks cluster with capi

The following is step by step process on how to deploy aws eks cluster with fargate nodes

## References

- [CAPI quickstart](https://cluster-api.sigs.k8s.io/user/quick-start)

- [CAPI Createing EKS cluster](https://cluster-api-aws.sigs.k8s.io/topics/eks/creating-a-cluster.html#creating-a-eks-cluster)

- [CAPI AWS ref](https://cluster-api-aws.sigs.k8s.io/crd/index.html)

## Install all required command line tool

- [kubectl](https://kubernetes.io/docs/tasks/tools/)

- [kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)

- [clusterctl](https://cluster-api.sigs.k8s.io/user/quick-start#install-clusterctl)

- [clusterawsadm](https://cluster-api.sigs.k8s.io/user/quick-start#initialization-for-common-providers)

## Create temp local bootstrap cluster

- CAPI requires an existing, known as the management cluster

- The kuberenetes cluster we create will be transformed into a mangement cluster

- We will first provision temp local bootstrat cluster which will be used to provision a targer management cluster

Note the steps followed here are more or less what is found in the [CAPI quick start](https://cluster-api.sigs.k8s.io/user/quick-start#quick-start) with certain added details

Create local kind cluster

```
kind create cluster
```

Init management cluser

```
AWS_B64ENCODED_CREDENTIALS=$(clusterawsadm bootstrap credentials encode-as-profile) \
                                                                            clusterctl \
                                                                              init \
                                                                              --config ./cluster/config/init.yaml \
                                                                              --infrastructure aws
```

## Create Workload Cluster Template

The workload template will be used to initialize the actual management cluster, for our example we will generate an [EKS cluster with a managed node group configuration](https://cluster-api-aws.sigs.k8s.io/topics/eks/creating-a-cluster.html#creating-a-eks-cluster), since we will be create and eks cluser with fargate node we will have to manually modify this template

To generate the template run:

```
clusterctl generate cluster capi-eks-quickstart --flavor eks-managedmachinepool --kubernetes-version v1.22.9 --worker-machine-count=3 > capi-eks-fargate.yaml
```
