# Kubernetes Lab

Deploys a Kubernetes Lab on the Rackspace Public Cloud.

```
openstack stack create                                                                              \
  --parameter prefix="my-k8s-lab"                                                                   \
  --parameter worker_node_count=3                                                                   \
  --template https://raw.githubusercontent.com/cloud-training/lab-kubernetes/master/heat/stack.yaml \
  my-stack-name
```
