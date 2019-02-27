# Troubleshooting Kubernetes Clusters.



   - Is the manifest correct? Check with the Kubernetes JSON schema.

   - Does the container run standalone, locally (that is, outside of Kubernetes)?

   - Can Kubernetes reach the container registry and actually pull the container image?

   - Can the nodes talk to each other?

   - Can the nodes reach the master?

   - Is DNS available in the cluster?

   - Are there sufficient resources available on the nodes?

   - Did you restrict the container’s resource usage?

# Common Issues

   - Pods in Pending, CrashLoopBackOff or Waiting state
      - If kubectl get pods shows that your pod status is Pending or CrashLoopBackOff, this means that the pod could not be scheduled on a node. Typically, this is because of insufficient CPU or memory resources. It could also arise due to the absence of a network overlay or a volume provider. 
      - If kubectl get pods shows that your pod status is ImagePullBackOff or ErrImagePull, this means that the pod could not run because it could not pull the image.
   - PVCs in Pending state
      - If kubectl get pvc shows that your PVC status is Pending the pod is unable to start because the cluster is unable to fulfil the request for a persistent volume and attach it to the container.
   - DNS lookup failures for exposed services
   - Non-responsive pods or containers
   - Authentication failures
   - Difficulties in finding the external IP address of a node


# Commands to run to gather information.

  - kubectl describe pods ${POD_NAME}
  - kubectl describe service ${SERVICE_NAME}
  - kubectl describe deployment ${DEPLOYMENT_NAME}
  - kubectl logs ${POD_NAME} ${CONTAINER_NAME}
  - kubectl logs --previous ${POD_NAME} ${CONTAINER_NAME}
  - kubectl exec ${POD_NAME} -c ${CONTAINER_NAME} -- ${CMD} ${ARG1} ${ARG2} ... ${ARGN}
  - kubectl exec cassandra -- cat /var/log/cassandra/system.log
  - kubectl create --validate -f mypod.yaml
  - kubectl get endpoints ${SERVICE_NAME}
  - kubectl log svc/svcname -f
  - kubectl run -i -t busybox --image=radial/busyboxplus:curl --restart=Never -- sh
  - kubectl cluster-info dump
 
# Where to search for answers.

```

If running in Azure on AKS - https://docs.microsoft.com/en-us/azure/aks/view-master-logs

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



