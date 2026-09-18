# Kubernetes & Helm Notes

_Conceptual notes — not yet hands-on. Prioritizing Docker/ECS fundamentals first_.

## What is Kubernetes, and why it exists

Docker Compose orchestrates multiple containers on **one machine** — fine for local development or a small app, but it has no answer for real production concerns: running across multiple servers, automatically restarting a crashed container on another machine, scaling under load, or rolling out updates without downtime.

**Kubernetes (K8s) is a container orchestration system** — it manages containers across a _cluster_ of machines (nodes), not just one. Core responsibilities:

- **Scheduling** — decides which node runs which container
- **Self-healing** — automatically restarts a failed container (pod), potentially on a different node
- **Scaling** — adds/removes container instances based on load
- **Rolling updates** — deploys new versions gradually, with automatic rollback on failure
- **Service discovery/networking** — containers can find and reach each other reliably even as they move between nodes

## kubectl — CLI for interacting with a cluster

| Command                                                          | Purpose                                             |
| ---------------------------------------------------------------- | --------------------------------------------------- |
| `kubectl get pods`                                               | List pods in the current namespace                  |
| `kubectl get nodes`                                              | List nodes in the cluster                           |
| `kubectl get services`                                           | List services in the cluster                        |
| `kubectl apply -f <file>.yaml`                                   | Apply configuration from a file (e.g. a deployment) |
| `kubectl create -f <file>.yaml`                                  | Create a resource from a file                       |
| `kubectl delete -f <file>.yaml`                                  | Delete a resource defined in a file                 |
| `kubectl exec -it <pod-name> -- bash`                            | Open a shell inside a running pod                   |
| `kubectl logs <pod-name>`                                        | View a pod's logs                                   |
| `kubectl describe pod <pod-name>`                                | Detailed info about a pod                           |
| `kubectl scale deployment <name> --replicas=<number>`            | Scale a deployment to N replicas                    |
| `kubectl rollout restart deployment <name>`                      | Restart a deployment                                |
| `kubectl port-forward pod <pod-name> <local-port>:<remote-port>` | Forward a local port to a pod                       |

## What is Helm, and why it's needed

Kubernetes config is written as YAML — for any real app, that's a lot of it, much of it repetitive across environments (dev/staging/prod) with only small differences (replica count, image tag, resource limits).

**Helm is a package manager for Kubernetes** — the same idea as `apt` for Ubuntu packages, but for K8s applications. A **Helm chart** is a templated bundle of that YAML with configurable values, so you install/upgrade/rollback an entire application's config as one versioned unit instead of hand-managing dozens of files.

| Command                                    | Purpose                 |
| ------------------------------------------ | ----------------------- |
| `helm install <release-name> <chart-name>` | Install a chart         |
| `helm upgrade <release-name> <chart-name>` | Upgrade a release       |
| `helm list`                                | List installed releases |
| `helm delete <release-name>`               | Delete a release        |
| `helm search <chart-name>`                 | Search for a chart      |

## Related concepts worth knowing exist (not yet covered in depth)

- **Pods, Deployments, Services** — the core K8s objects: a Pod runs one or more containers; a Deployment manages replica Pods; a Service exposes them at a stable network address
- **ConfigMaps and Secrets** — how K8s handles environment config and sensitive values, conceptually similar to `.env` files used with Docker Compose
- **Ingress** — how external traffic reaches services inside the cluster, conceptually similar to the nginx reverse-proxy role already used in the [University Research Portal](https://github.com/pritamdhurel-tech/University_Research_Portal_Security_Application) deployment
- **Namespaces** — logical separation within one cluster (e.g. dev/prod isolated in the same cluster)
- **EKS** — AWS's managed Kubernetes; the AWS-specific angle to prioritize once this topic is revisited
