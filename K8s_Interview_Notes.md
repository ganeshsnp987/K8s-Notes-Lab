# Kubernetes Interview Preparation Notes

## 1. Pods
- **What**: Smallest deployable unit in K8s
- **Key Points**: 
  - Contains one or more containers
  - Shared network and storage
  - Ephemeral (temporary)
- **Commands**: `kubectl get pods`, `kubectl describe pod <name>`
- **Use Cases**: Single container apps, sidecar patterns

## 2. Labels & Selectors
- **What**: Key-value pairs for organizing resources
- **Key Points**:
  - Labels identify resources
  - Selectors filter resources
  - Used by services, deployments
- **Example**: `app=nginx`, `env=prod`
- **Commands**: `kubectl get pods -l app=nginx`

## 3. ReplicationController (RC) & ReplicaSet (RS)
- **RC**: Old way to maintain pod replicas
- **RS**: New way, supports set-based selectors
- **Key Points**:
  - Ensures desired number of pods
  - Self-healing
  - RS used by Deployments
- **Difference**: RS has better selector support

## 4. Deployments
- **What**: Manages ReplicaSets and Pods
- **Key Features**:
  - Rolling updates
  - Rollback capability
  - Scaling
  - Pause/Resume
- **Commands**: `kubectl rollout status`, `kubectl rollout undo`
- **Strategies**: RollingUpdate, Recreate

## 5. Services
- **Types**:
  - **ClusterIP**: Internal cluster access (default)
  - **NodePort**: External access via node port
  - **LoadBalancer**: Cloud load balancer
  - **ExternalName**: DNS CNAME record
- **Key Points**: Stable IP, load balancing, service discovery

## 6. Storage
### Volumes
- **emptyDir**: Temporary storage
- **hostPath**: Node filesystem
- **configMap/secret**: Configuration data

### PV/PVC/StorageClass
- **PV**: Cluster storage resource
- **PVC**: User storage request
- **StorageClass**: Dynamic provisioning template
- **Binding**: PVC binds to suitable PV

### StatefulSets
- **What**: For stateful applications
- **Features**: Stable network identity, ordered deployment, persistent storage

## 7. ConfigMaps
- **What**: Store non-sensitive configuration
- **Usage**: Environment variables, config files, command args
- **Commands**: `kubectl create configmap`, mount as volume

## 8. Secrets
- **Types**:
  - **Generic**: Username/password
  - **TLS**: Certificates
  - **Docker-registry**: Image pull secrets
- **Key Points**: Base64 encoded, not encrypted by default

## 9. Namespaces
- **What**: Virtual clusters within cluster
- **Default Namespaces**: default, kube-system, kube-public
- **Use Cases**: Multi-tenancy, resource isolation
- **Commands**: `kubectl get ns`, `kubectl create ns`

## 10. Resource Quotas & Limits
- **ResourceQuota**: Limits resources per namespace
- **LimitRange**: Default/max limits for pods
- **Resource Types**: CPU, memory, storage, object counts

## 11. HPA & Metrics Server
- **HPA**: Horizontal Pod Autoscaler
- **Metrics**: CPU, memory, custom metrics
- **Requirements**: Metrics server, resource requests
- **Commands**: `kubectl autoscale`, `kubectl get hpa`

## 12. Jobs & Init Containers
### Jobs
- **Job**: Run pods to completion
- **CronJob**: Scheduled jobs
- **Use Cases**: Batch processing, backups

### Init Containers
- **What**: Run before main containers
- **Use Cases**: Setup, prerequisites, data loading

## 13. Helm
- **What**: Package manager for K8s
- **Components**: Charts, releases, repositories
- **Commands**: `helm install`, `helm upgrade`, `helm rollback`
- **Benefits**: Templating, versioning, easy deployment

## 14. Taints & Tolerations
- **Taints**: Applied to nodes to repel pods
- **Tolerations**: Applied to pods to tolerate taints
- **Effects**: NoSchedule, PreferNoSchedule, NoExecute
- **Use Cases**: Dedicated nodes, node maintenance

## 15. DaemonSets
- **What**: Runs one pod per node
- **Use Cases**: Logging agents, monitoring, network plugins
- **Key Points**: Automatically scales with cluster

## 16. Static Pods
- **What**: Managed by kubelet, not API server
- **Location**: `/etc/kubernetes/manifests/`
- **Use Cases**: Control plane components
- **Key Points**: Cannot be deleted via kubectl

## 17. Custom Scheduler
- **What**: Alternative to default scheduler
- **Use Cases**: Special scheduling requirements
- **Implementation**: Custom logic for pod placement

## 18. Network Policies
- **What**: Firewall rules for pods
- **Types**: Ingress, Egress
- **Selectors**: podSelector, namespaceSelector
- **Default**: All traffic allowed without policies

## 19. RBAC (Role-Based Access Control)
- **Components**:
  - **Role/ClusterRole**: Permissions
  - **RoleBinding/ClusterRoleBinding**: Assign roles
- **Subjects**: Users, groups, service accounts
- **Verbs**: get, list, create, update, delete

## 20. Service Accounts
- **What**: Identity for pods to access API
- **Default**: Every namespace has default SA
- **Usage**: Mount tokens, RBAC subjects
- **Commands**: `kubectl create sa`

## 21. Security Context
- **What**: Security settings for pods/containers
- **Settings**: runAsUser, runAsGroup, fsGroup, capabilities
- **Levels**: Pod level, container level

## 22. Ingress
- **What**: HTTP/HTTPS routing to services
- **Components**: Ingress resource, Ingress controller
- **Features**: SSL termination, path-based routing, host-based routing
- **Controllers**: Nginx, Traefik, HAProxy

## Common Interview Questions

### Architecture
- **Master Components**: API server, etcd, scheduler, controller manager
- **Node Components**: kubelet, kube-proxy, container runtime
- **Add-ons**: DNS, dashboard, monitoring

### Troubleshooting
- **Pod Issues**: Check logs, describe pod, check resources
- **Service Issues**: Check endpoints, labels, selectors
- **Network Issues**: Check network policies, DNS

### Best Practices
- Use resource limits and requests
- Implement health checks (liveness/readiness probes)
- Use namespaces for isolation
- Implement RBAC
- Use secrets for sensitive data
- Regular backups of etcd

### Commands Cheat Sheet
```bash
# Pods
kubectl get pods -o wide
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- /bin/bash

# Deployments
kubectl create deployment nginx --image=nginx
kubectl scale deployment nginx --replicas=3
kubectl rollout restart deployment nginx

# Services
kubectl expose deployment nginx --port=80 --type=ClusterIP
kubectl get svc

# ConfigMaps & Secrets
kubectl create configmap myconfig --from-literal=key=value
kubectl create secret generic mysecret --from-literal=password=secret

# Debugging
kubectl describe <resource> <name>
kubectl get events --sort-by=.metadata.creationTimestamp
```

## Quick Revision Tips
1. **Practice kubectl commands** - Most interviews include practical tasks
2. **Understand YAML structure** - Be able to write basic manifests
3. **Know the differences** - Deployment vs StatefulSet, Service types
4. **Security concepts** - RBAC, Network Policies, Security Context
5. **Troubleshooting** - Common issues and how to debug them
