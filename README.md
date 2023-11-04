# The Observability Stack for Kubernetes.

All bells and whistles included an observability stack for Kubernetes. 
* [Kube-Prometheus](https://github.com/prometheus-operator/kube-prometheus#kube-prometheus) the Kubernetes monitoring stack
  * [Prometheus](https://github.com/prometheus/prometheus) to collect metrics
  * [AlertManager](https://github.com/prometheus/alertmanager#alertmanager-) to fire the alerts
  * [Grafana](https://github.com/grafana/grafana) to visualize what's going on
  * [Node-Exporter](https://github.com/prometheus/node_exporter) to export metrics from the nodes
  * [Kube-State-Metrics](https://github.com/kubernetes/kube-state-metrics) to get metrics from kubernetes api-server
  * [Prometheus-Operator](https://github.com/prometheus-operator/prometheus-operator#prometheus-operator) to manage the life-cycle of Prometheus and AlertManager custom resource definitions (CRDs)
* [Loki](https://github.com/grafana/loki) for the logs
* [Tempo](https://github.com/grafana/tempo) distributed tracing 

For a quick start, run `helmfile apply`. In the `helmfile.yaml` is a template for the helm deployments. This can be modified to suit your needs.

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

This will not clean up all the crds created, [please refer](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/README.md)  on how to delete all the crds.
