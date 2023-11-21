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
1. Install `helmfile`, ref, https://github.com/helmfile/helmfile 
2. Install `helm-diff` by running `helm plugin install https://github.com/databus23/helm-diff`

If you encounter 'no matches for kind' errors when applying helmfile,
use `--skip-diff-on-install` flag to bypass the initial diff when running apply:
```
helmfile apply --skip-diff-on-install
```


This should bring up `Grafana`, `Prometheus`, `Loki` and `Tempo`. 
The Grafana from `kube-prometheus` stack will by default have the `Prometheus` and `Alert-manager` data sources enabled.
We are also adding the extra datasource for `Loki` and `Tempo` to `Grafana` from [values/kube-prometheus-stack.yaml.gotmpl](values/kube-prometheus-stack.yaml.gotmpl). 

Ideally, if everything is good, we should see the below services,
```
k get svc
NAME                      TYPE           CLUSTER-IP      EXTERNAL-IP    PORT(S)                                                                                                   AGE
alertmanager-operated     ClusterIP      None            <none>         9093/TCP,9094/TCP,9094/UDP                                                                                3d16h
grafana                   LoadBalancer   10.43.86.20     10.96.194.35   80:30205/TCP                                                                                              3d16h
kube-state-metrics        ClusterIP      10.43.91.244    <none>         8080/TCP                                                                                                  3d16h
loki                      ClusterIP      10.43.204.28    <none>         3100/TCP                                                                                                  3d16h
loki-headless             ClusterIP      None            <none>         3100/TCP                                                                                                  3d16h
loki-memberlist           ClusterIP      None            <none>         7946/TCP                                                                                                  3d16h
node-exporter             ClusterIP      10.43.195.165   <none>         9100/TCP                                                                                                  3d16h
prometheus-alertmanager   ClusterIP      10.43.229.249   <none>         9093/TCP,8080/TCP                                                                                         3d16h
prometheus-operated       ClusterIP      None            <none>         9090/TCP                                                                                                  3d16h
prometheus-operator       ClusterIP      10.43.114.50    <none>         443/TCP                                                                                                   3d16h
prometheus-prometheus     ClusterIP      10.43.200.3     <none>         9090/TCP,8080/TCP                                                                                         3d16h
tempo                     ClusterIP      10.43.66.58     <none>         3100/TCP,6831/UDP,6832/UDP,14268/TCP,14250/TCP,9411/TCP,55680/TCP,55681/TCP,4317/TCP,4318/TCP,55678/TCP   3d16h
```

Grafana can be accessed with the loadbalancer ip, if your cluster doesn't support LoadBalancer, you can always port forward the grafana service/pod.

 We will have to add the data sources for `Loki` and `Tempo`, take a look at 

_Note_:
_if the alert manager and prometheus pods are going to restart the loop, it's most likely that you have prometheus already running in the cluster. You will have to delete those._


## Tracing with Hot R.O.D
`Hotrod` is a popular demo app that uses OpenTracing API, you may find [more details about the app here](https://github.com/uber/jaeger/tree/main/examples/hotrod)
For the demo, let's deploy it to the same namespace, 
```
k apply -f hotrod-sample-app.yml

# let's check the deployment and service
k get deploy,po,svc -l app=hotrod
NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hotrod   1/1     1            1           3d

NAME                         READY   STATUS    RESTARTS   AGE
pod/hotrod-fcd546f58-zsmcx   1/1     Running   0          3d

NAME             TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
service/hotrod   LoadBalancer   10.43.209.194   10.96.196.137   80:30743/TCP   3d

```

Access the Hotrod using loadbalancer ip or `port-forward` to your local. Click on a few rides from the UI and head over to Grafana. Go to explore view and select Loki as the data source, from the filer use _app=hotrod_

## Distributed Tracing straight from the Loki logs to Tempo

![image](https://github.com/govindkailas/k8s-observability-stack/assets/6698224/f129e7d3-1e66-4ce4-b488-7ebb70e22926)

## Datasources in Grafana
![image](https://github.com/govindkailas/k8s-observability-stack/assets/6698224/90b9ffe1-0f24-494d-ae71-bff2a54e4fc0)

## Cleanup 

To delete the observability stack, 
```
helmfile delete
```

This will not clean up all the CRDs created, [please refer](https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/README.md)  on how to delete all the CRDs.



