Here’s a cleaned-up, simplified, and more interview-focused version of your **K8s Notes** (`k8s-notes.md`).
I kept the `.md` format, made the wording simpler, and added missing but commonly asked interview topics (like VPA, CNI plugins, service mesh, logging, etc.).

You can directly use this as your **final prep guide**.

---

# Kubernetes Interview Preparation Notes

## 1. Pods

* **What**: Smallest unit in Kubernetes
* **Key Points**:

  * Runs one or more containers together
  * Share same **network IP** and **storage volumes**
  * Pods are **temporary** (ephemeral)
* **Commands**:

  ```bash
  kubectl get pods
  kubectl describe pod <pod-name>
  kubectl logs <pod-name>
  ```
* **Use Cases**: Single container app, sidecar (logging, proxy, monitoring)

---

## 2. Labels & Selectors

* **Labels**: Key-value pairs to identify objects (`app=nginx`, `env=prod`)
* **Selectors**: Query/filter objects using labels
* **Usage**: Services, Deployments, Network Policies
* **Command**:

  ```bash
  kubectl get pods -l app=nginx
  ```

---

## 3. ReplicaSet (RS) & ReplicationController (RC)

* **RC**: Old way, replaced by RS
* **RS**: New, supports set-based selectors
* **Purpose**: Keep the desired number of pods running
* **Used by**: Deployments

---

## 4. Deployments & Strategies

* **Deployment**: Manages ReplicaSets and Pods
* **Features**: Rolling updates, rollback, scaling, pause/resume

### Strategies

1. **Rolling Update** (default) → Zero downtime, gradual rollout
2. **Recreate** → Stops all old pods, then starts new (causes downtime)
3. **Blue-Green** → Two environments, switch traffic instantly
4. **Canary** → Release to small group first, then expand
5. **A/B Testing** → Different versions for different user groups

---

## 5. Services

* **Types**:

  * **ClusterIP** → Internal only (default)
  * **NodePort** → Exposes via `<NodeIP>:<Port>`
  * **LoadBalancer** → Uses Cloud LB
  * **ExternalName** → Maps service to external DNS
* **Purpose**: Stable IP, load balancing, service discovery

---

## 6. Storage

### Volumes

* **emptyDir** → Temporary storage (deleted with pod)
* **hostPath** → Uses node’s local filesystem
* **configMap/secret** → Mount config/secrets

### PV / PVC / StorageClass

* **PV**: Actual cluster storage resource
* **PVC**: User request for storage
* **StorageClass**: Auto-create PVs (dynamic provisioning)

### StatefulSets

* For **stateful apps** (databases, Kafka, etc.)
* Features: **Stable identity + persistent storage**

---

## 7. ConfigMaps

* **What**: Store non-sensitive config (key-value)
* **Usage**: Env vars, config files, volume mounts
* **Command**:

  ```bash
  kubectl create configmap app-config --from-literal=key=value
  ```

---

## 8. Secrets

* **What**: Store sensitive data (passwords, tokens, certs)
* **Types**: Generic, TLS, Docker-registry
* **Note**: Base64 encoded (not encrypted by default)

---

## 9. Namespaces

* **What**: Virtual clusters inside a cluster
* **Default namespaces**: `default`, `kube-system`, `kube-public`
* **Use Cases**: Multi-team, isolation, resource separation

---

## 10. Resource Management

* **ResourceQuota**: Limits for namespace
* **LimitRange**: Default/max for pods
* **Requests**: Min guaranteed resources
* **Limits**: Max allowed resources
* **QoS Classes**: Guaranteed, Burstable, BestEffort

---

## 11. Autoscaling

* **HPA**: Horizontal Pod Autoscaler → scales pods based on CPU/memory/custom metrics
* **VPA**: Vertical Pod Autoscaler → adjusts pod resource requests/limits
* **Cluster Autoscaler**: Adds/removes nodes in cloud

---

## 12. Jobs & Init Containers

* **Job**: Run task until completion (batch)
* **CronJob**: Scheduled job
* **Init Container**: Runs before app container (setup tasks)

---

## 13. Helm

* **What**: Package manager for Kubernetes
* **Benefits**: Templates, version control, easy upgrades
* **Commands**: `helm install`, `helm upgrade`, `helm rollback`

---

## 14. Taints & Tolerations

* **Taint** → Applied on node to block pods
* **Toleration** → Allows pod to bypass taint
* **Effects**: `NoSchedule`, `PreferNoSchedule`, `NoExecute`

---

## 15. DaemonSets

* Runs **one pod on each node**
* **Use Cases**: Logging agents, monitoring (Prometheus, Fluentd), network plugins

---

## 16. Static Pods

* Created by **kubelet directly**, not API server
* Located at `/etc/kubernetes/manifests/`
* Used for control plane components

---

## 17. Networking

* **Pod-to-Pod**: Flat network (unique IPs)
* **CNI Plugins**: Flannel, Calico, Weave
* **Network Policies**: Restrict traffic (Ingress/Egress rules)
* **Ingress**: HTTP/HTTPS routing with SSL termination

---

## 18. RBAC & Service Accounts

* **RBAC**: Roles + RoleBindings (namespace), ClusterRole + ClusterRoleBinding (cluster-wide)
* **Service Account**: Identity for pods to access API

---

## 19. Security

* **Security Context**: Run as user, set permissions, capabilities
* **Pod Security Standards**: Restricted, Baseline, Privileged
* **Image Security**: Always scan images before use
* **Secrets**: Store sensitive data securely

---

## 20. Monitoring & Logging

* **Metrics**: Prometheus + Grafana
* **Logs**: EFK (Elasticsearch + Fluentd + Kibana)
* **Tracing**: Jaeger, Zipkin
* **kubectl**:

  ```bash
  kubectl top pods
  kubectl top nodes
  ```

---

## 21. Operators & CRDs

* **CRD**: Extend Kubernetes with custom objects
* **Operator**: Controller that manages CRDs
* **Use Cases**: Databases, Kafka, Monitoring tools

---

## 22. Health Checks (Probes)

* **Liveness**: Restart container if unhealthy
* **Readiness**: Remove pod from service if not ready
* **Startup**: For slow-starting apps

---

## 23. Affinity & Anti-Affinity

* **Node Affinity**: Schedule pod on specific nodes
* **Pod Affinity**: Co-locate with other pods
* **Pod Anti-Affinity**: Spread across nodes

---

## 24. Backup & Disaster Recovery

* **etcd Backup** → Most important
* **Tools**: Velero for resource + PV backups
* **Best Practice**: Regular snapshots, test restore

---

## 25. Service Mesh (Advanced)

* **What**: Manage microservice-to-microservice traffic
* **Examples**: Istio, Linkerd
* **Features**: Traffic routing, mTLS, observability

---

## 26. Common Troubleshooting

* **Pod Pending** → Not enough resources, PVC not bound, taints
* **CrashLoopBackOff** → Check logs, resource limits, probes
* **Service not reachable** → Check endpoints, selectors, network policies
* **High usage** → Check HPA, node capacity

---

## Quick Revision Checklist

* [ ] Core objects → Pod, Service, Deployment, ConfigMap, Secret
* [ ] Storage → PV, PVC, StatefulSets
* [ ] Networking → Service types, Ingress, Policies
* [ ] Security → RBAC, SecurityContext, PodSecurity
* [ ] Scaling → HPA, VPA, Cluster Autoscaler
* [ ] Monitoring → Prometheus, Grafana, Logs
* [ ] Best practices → Resource limits, probes, RBAC, secrets
* [ ] Troubleshooting → Pod Pending, CrashLoop, Service issues
* [ ] YAML → Write manifests from scratch

---

✅ This version is **interview-focused**, in **simpler words**, and covers missing but important areas like VPA, CNI plugins, service mesh, and logging.

Would you like me to also create a **one-page ultra-short cheat sheet** (just key commands + one-line concepts) for last-minute revision?
