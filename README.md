# Prometheus Federation And Thanos [Kubernetes Sri Lanka Webinar]

## Assumptions and prerequisites

### You have three separate multi-node Kubernetes clusters running on the same cloud provider:

- Two “data producer” clusters which will host Prometheus deployments and applications that expose metrics via Prometheus.

- One “data aggregator” cluster which will host Thanos and aggregate the data from the data producers. This cluster will also host Grafana for data visualization and reporting.

- You have the kubectl CLI and the Helm v3.x package manager installed and configured to work with your Kubernetes clusters.

