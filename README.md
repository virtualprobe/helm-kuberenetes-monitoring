# Helm Charts for Prometheus and Kepler

This repository contains two Helm charts:

1. **Prometheus**: A chart for deploying Prometheus and the related monitoring stack.
2. **Kepler**: A chart for deploying Kepler, a tool for monitoring energy and resource usage in Kubernetes.

## Prerequisites

Before deploying the Helm charts, ensure the following prerequisites are met:

- A Kubernetes cluster is up and running.
- [`kubectl`](https://kubernetes.io/docs/tasks/tools/) is installed and configured to interact with your cluster.
- [`Helm`](https://helm.sh/docs/intro/install/) is installed.

## Installing Helm

Follow these steps to install Helm on your system:

1. Download the Helm binary for your platform from the [Helm releases page](https://github.com/helm/helm/releases).
2. Extract the binary:
   ```bash
   tar -zxvf helm-vX.Y.Z-linux-amd64.tar.gz
   ```
   Replace `X.Y.Z` with the desired version.
3. Move the binary to a location in your system's `PATH`:
   ```bash
   mv linux-amd64/helm /usr/local/bin/helm
   ```
4. Verify the installation:
   ```bash
   helm version
   ```

## Configuring Helm with `kubectl`

To ensure Helm can communicate with your Kubernetes cluster, set up the configuration as follows:

1. Verify your `kubectl` setup:
   ```bash
   kubectl get nodes
   ```
   This command should return a list of nodes in your Kubernetes cluster.

2. Initialize Helm:
   ```bash
   helm repo add stable https://charts.helm.sh/stable
   helm repo update
   ```

## Authenticating to the Private Docker Repository

Before deploying Kepler, authenticate to the private Docker repository `virtualprobe/kepler`:

```bash
docker login -u <username> -p <token>
```

Replace `<username>` with your Docker username and `<token>` with the access token provided.

Once authenticated, pull the Kepler image locally to verify you have access:

```bash
docker pull virtualprobe/kepler
```

## Deploying the Helm Charts

### Deploying Prometheus

To deploy Prometheus using the `kube-prometheus-stack` chart:

```bash
helm upgrade --install prometheus ./kube-prometheus-stack/ \
  --namespace monitoring \
  --create-namespace \
  -f kube-prometheus-stack/values.yaml
```

- **Namespace**: `monitoring`
- **Values file**: `kube-prometheus-stack/values.yaml`

This command will install or upgrade Prometheus and the related monitoring components in the `monitoring` namespace.

### Deploying Kepler

To deploy Kepler:

```bash
helm upgrade --install kepler ./kepler/ \
  --namespace kepler \
  --create-namespace \
  -f kepler/values.yaml
```

- **Namespace**: `kepler`
- **Values file**: `kepler/values.yaml`

This command will install or upgrade Kepler in the `kepler` namespace.

## Verifying Deployments

### Verify Prometheus

1. Check the status of Prometheus pods:
   ```bash
   kubectl get pods -n monitoring
   ```

2. Access the Prometheus UI:
   - Use a port-forward command:
     ```bash
     kubectl port-forward svc/prometheus-kube-prometheus-stack-prometheus 9090 -n monitoring
     ```
   - Open your browser and navigate to [http://localhost:9090](http://localhost:9090).

### Verify Kepler

1. Check the status of Kepler pods:
   ```bash
   kubectl get pods -n kepler
   ```

2. Ensure that Prometheus is successfully scraping Kepler metrics.

## Troubleshooting

- If the deployments fail, check the logs of the pods:
  ```bash
  kubectl logs <pod-name> -n <namespace>
  ```

- Ensure your Kubernetes cluster has sufficient resources (CPU, memory, storage).

## References

- [Helm Documentation](https://helm.sh/docs/)
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Kepler Documentation](https://github.com/sustainable-computing-io/kepler)

