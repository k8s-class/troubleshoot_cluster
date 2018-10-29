# Troubleshooting Kubernetes Clusters.

  - commands to run
  - Where to search for answers
  - How to improve on the base install

# Commands to run to gather information.

  - kubectl describe pods ${POD_NAME}
  - kubectl logs ${POD_NAME} ${CONTAINER_NAME}
  - kubectl logs --previous ${POD_NAME} ${CONTAINER_NAME}
  - kubectl exec ${POD_NAME} -c ${CONTAINER_NAME} -- ${CMD} ${ARG1} ${ARG2} ... ${ARGN}
  - kubectl exec cassandra -- cat /var/log/cassandra/system.log
  - kubectl create --validate -f mypod.yaml
  - kubectl get endpoints ${SERVICE_NAME}
  - kubectl log svc/svcname -f
 
# Where to search for answers.

```
SSH into master and worker nodes to look at logs. Use journalctl -u kubelet

### Master

    /var/log/kube-apiserver.log - API Server, responsible for serving the API
    /var/log/kube-scheduler.log - Scheduler, responsible for making scheduling decisions
    /var/log/kube-controller-manager.log - Controller that manages replication controllers

### Worker Nodes

    /var/log/kubelet.log - Kubelet, responsible for running containers on the node
    /var/log/kube-proxy.log - Kube Proxy, responsible for service load balancing
```

```
SSH into the worker nodes and do a docker stats to find CPU and Memory usage. or kubectl top nodes/pods if you have metric server setup.
```


# How to improve on the base cluster.

- [Control CPU Management Policies on the Node](https://kubernetes.io/docs/tasks/administer-cluster/cpu-management-policies/) - This minimizes over provisioning the node.
- Enforce CPU limits on all containers. This ensure nodes are not over provisioned (in combination with the previous point) and that pods always receive the required CPU.
- Better estimate node vertical scale in relation to pods that eventually land on them. Not all pods are created equally, some are more CPU intensive than others.
- Consider bottlenecks when changing replica counts. If component A calls component B; simply doubling replicas counts for A will most likely not resolve issues in component B.
- Setup centralized logging.



