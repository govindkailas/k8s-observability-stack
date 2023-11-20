# The Observability Stack for Kubernetes.

All bells and whistles included observability stack for Kubernetes. 
* [Kube-Prometheus](https://github.com/prometheus-operator/kube-prometheus#kube-prometheus) the Kubernetes monitoring stack
  * [Prometheus](https://github.com/prometheus/prometheus) to collect metrics
  * [AlertManager](https://github.com/prometheus/alertmanager#alertmanager-) to fire the alerts
  * [Grafana](https://github.com/grafana/grafana) to visualize what's going on
  * [Node-Exporter](https://github.com/prometheus/node_exporter) to export metrics from the nodes
  * [Kube-State-Metrics](https://github.com/kubernetes/kube-state-metrics) to get metrics from kubernetes api-server
  * [Prometheus-Operator](https://github.com/prometheus-operator/prometheus-operator#prometheus-operator) to manage the life-cycle of Prometheus and AlertManager custom resource definitions (CRDs)
* [Loki](https://github.com/grafana/loki) for the logs
* [Tempo](https://github.com/grafana/tempo) distributed tracing 

For a quick start, run `helmfile apply`. The [helmfile.yaml](helmfile.yaml) is a template for the helm deployments. This can be modified to suit your needs.

## Notes

## Prerequisites
1. Install helmfile, ref, https://github.com/helmfile/helmfile 
2. Install helm-diff by running `helm plugin install https://github.com/databus23/helm-diff`

If you encounter 'no matches for kind' errors when applying helmfile,
use `--skip-diff-on-install` flag to bypass the initial diff when running apply:
```
helmfile apply --skip-diff-on-install
```



To delete the observability stack, 
```
helmfile delete
```

This will not clean up all the crds created, [please refer](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/README.md)  on how to delete all the cards.

## Distributed Tracing straight from the Loki logs to Tempo

![image](https://github.com/govindkailas/k8s-observability-stack/assets/6698224/f129e7d3-1e66-4ce4-b488-7ebb70e22926)

## Datasources in Grafana
![image](https://github.com/govindkailas/k8s-observability-stack/assets/6698224/90b9ffe1-0f24-494d-ae71-bff2a54e4fc0)

