# DevLake Lab Configuration

Helm values for deploying Apache DevLake to OpenShift lab environment.

## Structure

```
values/
└── devlake-lab-values.yaml    # Helm values for lab deployment
```

## Usage

### ArgoCD ApplicationSet

This repository is designed to be used as a values overlay in an ArgoCD ApplicationSet:

```yaml
sources:
  # Source 1: DevLake Helm chart
  - repoURL: https://apache.github.io/incubator-devlake-helm-chart
    targetRevision: 1.0.3-beta6
    chart: devlake
    helm:
      releaseName: 'devlake-lab'
      valueFiles:
        - $values/values/devlake-lab-values.yaml

  # Source 2: Custom values (this repo)
  - repoURL: https://github.com/vimalkansal/devlake-lab-config.git
    targetRevision: main
    ref: values
```

### Direct Helm Installation

```bash
# Add DevLake Helm repository
helm repo add devlake https://apache.github.io/incubator-devlake-helm-chart
helm repo update

# Install with custom values
helm install devlake devlake/devlake \
  --version 1.0.3-beta6 \
  --namespace devlake \
  --create-namespace \
  -f values/devlake-lab-values.yaml
```

## Configuration Highlights

- **Service Type**: ClusterIP (OpenShift-compatible)
- **Database**: Internal MySQL with persistent storage
- **Grafana**: Enabled with default credentials
- **Storage**: 10Gi with gp3-csi storage class

## OpenShift Routes

The Helm chart may not natively support OpenShift Routes. Create them manually:

```bash
# DevLake UI Route
oc create route edge devlake-ui \
  --service=devlake-ui \
  --namespace=devlake

# Grafana Route
oc create route edge devlake-grafana \
  --service=devlake-grafana \
  --namespace=devlake
```

## Security Notes

⚠️ **Change default passwords in production!**

- Grafana admin password: `admin`
- MySQL root password: `devlake`
- Encryption secret: Rotate for production use

## References

- [Apache DevLake](https://devlake.apache.org/)
- [DevLake Helm Chart](https://github.com/apache/incubator-devlake-helm-chart)
- [OpenShift Routes](https://docs.openshift.com/container-platform/latest/networking/routes/route-configuration.html)
