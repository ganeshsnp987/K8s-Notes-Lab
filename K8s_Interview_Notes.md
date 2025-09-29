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

## 4. Deployments & Rolling Strategies
- **What**: Manages ReplicaSets and Pods
- **Key Features**:
  - Rolling updates
  - Rollback capability
  - Scaling
  - Pause/Resume
- **Commands**: `kubectl rollout status`, `kubectl rollout undo`

### Deployment Strategies
#### 1. Rolling Update (Default)
- **Process**: Gradually replace old pods with new ones
- **Parameters**: 
  - `maxUnavailable`: Max pods that can be unavailable
  - `maxSurge`: Max pods above desired replica count
- **Pros**: Zero downtime, gradual rollout
- **Cons**: Mixed versions during update

#### 2. Recreate
- **Process**: Kill all old pods, then create new ones
- **Use Case**: When mixed versions can't coexist
- **Pros**: Simple, clean state
- **Cons**: Downtime during update

#### 3. Blue-Green Deployment
- **Process**: Deploy new version alongside old, switch traffic
- **Implementation**: Two identical environments
- **Pros**: Instant rollback, no mixed versions
- **Cons**: Double resource usage

#### 4. Canary Deployment
- **Process**: Deploy to small subset, gradually increase
- **Implementation**: Multiple deployments with traffic splitting
- **Pros**: Risk mitigation, gradual validation
- **Cons**: Complex setup, requires monitoring

#### 5. A/B Testing
- **Process**: Run multiple versions simultaneously
- **Use Case**: Feature testing with user segments
- **Implementation**: Ingress rules, service mesh
- **Pros**: Data-driven decisions
- **Cons**: Complex traffic management

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

### Rolling Strategy Examples
```yaml
# Rolling Update Configuration
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  replicas: 3
  template:
    spec:
      containers:
      - name: nginx
        image: nginx:1.20

# Recreate Strategy
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deployment
spec:
  strategy:
    type: Recreate
  replicas: 3
```

### Commands Cheat Sheet
```bash
# Pods
kubectl get pods -o wide
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- /bin/bash

# Deployments & Rolling Updates
kubectl create deployment nginx --image=nginx
kubectl scale deployment nginx --replicas=3
kubectl set image deployment/nginx nginx=nginx:1.21
kubectl rollout status deployment/nginx
kubectl rollout history deployment/nginx
kubectl rollout undo deployment/nginx
kubectl rollout pause deployment/nginx
kubectl rollout resume deployment/nginx

# Services
kubectl expose deployment nginx --port=80 --type=ClusterIP
kubectl get svc

# ConfigMaps & Secrets
kubectl create configmap myconfig --from-literal=key=value
kubectl create secret generic mysecret --from-literal=password=secret

# Node Management
kubectl get nodes
kubectl describe node <node-name>
kubectl cordon <node-name>  # Mark unschedulable
kubectl drain <node-name>   # Evict pods

# Resource Management
kubectl top nodes
kubectl top pods
kubectl describe quota

# Troubleshooting
kubectl describe <resource> <name>
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl logs <pod-name> --previous  # Previous container logs

# RBAC
kubectl auth can-i <verb> <resource>
kubectl get rolebindings,clusterrolebindings

# Networking
kubectl get endpoints
kubectl port-forward <pod-name> 8080:80
```

## Advanced Interview Topics

### 35. Kubernetes Upgrades
- **Strategy**: Rolling upgrade of control plane first, then nodes
- **Tools**: kubeadm upgrade, managed services auto-upgrade
- **Considerations**: API deprecations, backup before upgrade
- **Testing**: Validate applications after upgrade

### 36. Performance Tuning
- **Resource Optimization**: Right-sizing requests/limits
- **Node Optimization**: Kernel parameters, container runtime
- **Network Performance**: CNI selection, bandwidth limits
- **Storage Performance**: Storage class selection, IOPS

### 37. GitOps & CI/CD
- **GitOps**: Git as source of truth for deployments
- **Tools**: ArgoCD, Flux for continuous deployment
- **Pipeline Integration**: Jenkins, GitLab CI, GitHub Actions
- **Best Practices**: Separate config repos, automated testing

### 38. Cost Optimization
- **Resource Efficiency**: Vertical Pod Autoscaler (VPA)
- **Spot Instances**: Use for non-critical workloads
- **Cluster Optimization**: Right-size nodes, remove unused resources
- **Monitoring**: Track resource utilization and costs

## 23. Health Checks (Probes)
- **Liveness Probe**: Restart container if unhealthy
- **Readiness Probe**: Remove from service if not ready
- **Startup Probe**: For slow-starting containers
- **Types**: HTTP, TCP, Exec
- **Parameters**: initialDelaySeconds, periodSeconds, timeoutSeconds

## 24. Node Affinity & Anti-Affinity
- **Node Affinity**: Schedule pods on specific nodes
- **Types**: requiredDuringScheduling, preferredDuringScheduling
- **Pod Affinity/Anti-Affinity**: Schedule based on other pods
- **Use Cases**: Co-location, spreading across zones

## 25. Resource Management
- **Requests**: Minimum guaranteed resources
- **Limits**: Maximum allowed resources
- **QoS Classes**: Guaranteed, Burstable, BestEffort
- **OOMKilled**: When container exceeds memory limit

## 26. Cluster Autoscaler
- **What**: Automatically scales cluster nodes
- **Triggers**: Pending pods, underutilized nodes
- **Cloud Integration**: Works with cloud providers
- **Considerations**: Node groups, scaling policies

## 27. Monitoring & Observability
- **Metrics**: Prometheus, Grafana
- **Logging**: Fluentd, ELK stack
- **Tracing**: Jaeger, Zipkin
- **Key Metrics**: CPU, memory, network, disk

## 28. Multi-Container Patterns
- **Sidecar**: Helper container (logging, proxy)
- **Ambassador**: Proxy to external services
- **Adapter**: Transform data format
- **Init Container**: Setup before main container

## 29. Kubernetes API & Objects
- **API Groups**: core, apps, extensions
- **API Versions**: v1, v1beta1, v1alpha1
- **Object Structure**: apiVersion, kind, metadata, spec, status
- **Custom Resources**: Extend K8s API

## 30. Operators & CRDs
- **CRD**: Custom Resource Definition
- **Operator**: Controller for custom resources
- **Operator Pattern**: Automate application management
- **Examples**: Database operators, monitoring operators

## 31. Backup & Disaster Recovery
- **etcd Backup**: Critical for cluster state
- **Velero**: Backup tool for K8s resources
- **Strategies**: Regular snapshots, cross-region replication
- **Recovery**: Restore from backups, rebuild cluster

## 32. Kubernetes Networking
- **CNI**: Container Network Interface
- **Pod-to-Pod**: Flat network, unique IPs
- **Service Mesh**: Istio, Linkerd for advanced networking
- **DNS**: CoreDNS for service discovery

## 33. Security Best Practices
- **Pod Security Standards**: Restricted, Baseline, Privileged
- **Image Security**: Scan for vulnerabilities
- **Admission Controllers**: Validate/mutate resources
- **Network Segmentation**: Isolate workloads

## 34. Troubleshooting Scenarios
### Pod Stuck in Pending
- Check node resources, taints/tolerations, PVC binding
### Pod CrashLoopBackOff
- Check logs, liveness probe, resource limits
### Service Not Accessible
- Verify endpoints, labels, network policies
### High Resource Usage
- Check resource requests/limits, HPA configuration

## Quick Revision Tips
1. **Practice kubectl commands** - Most interviews include practical tasks
2. **Understand YAML structure** - Be able to write basic manifests
3. **Know the differences** - Deployment vs StatefulSet, Service types
4. **Security concepts** - RBAC, Network Policies, Security Context
5. **Troubleshooting** - Common issues and how to debug them
6. **Architecture questions** - Control plane, data plane components
7. **Scaling strategies** - HPA, VPA, Cluster Autoscaler
8. **Networking concepts** - CNI, Service Mesh, Ingress
9. **Storage patterns** - StatefulSets, PV lifecycle
10. **Security hardening** - Pod Security, RBAC, Network Policies
