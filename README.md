# Prometheus Federation And Thanos [Kubernetes Sri Lanka Webinar]

## Assumptions and prerequisites

### You have three separate multi-node Kubernetes clusters running on the same cloud provider:

- Two “data producer” clusters which will host Prometheus deployments and applications that expose metrics via Prometheus.

- One “data aggregator” cluster which will host Thanos and aggregate the data from the data producers. This cluster will also host Grafana for data visualization and reporting.

- You have the kubectl CLI and the Helm v3.x package manager installed and configured to work with your Kubernetes clusters.

### Step 1: Install the Prometheus Operator on each cluster

Bitnami’s Prometheus Operator chart provides easy monitoring definitions for Kubernetes services and management of Prometheus instances. It also includes an optional Thanos sidecar container, which can be used by your Thanos deployment to access cluster metrics.

Only one instance of the Prometheus Operator component should be running in a cluster.

- Add the Bitnami charts repository to Helm:
``` helm repo add bitnami https://charts.bitnami.com/bitnami ```

- Install the Prometheus Operator in the first “data producer” cluster using the command below:
```
helm install prometheus-operator \
  --set prometheus.thanos.create=true \
  --set operator.service.type=ClusterIP \
  --set prometheus.service.type=ClusterIP \
  --set alertmanager.service.type=ClusterIP \
  --set prometheus.thanos.service.type=LoadBalancer \
  --set prometheus.externalLabels.cluster="data-producer-0" \
  bitnami/prometheus-operator
```
 

 
