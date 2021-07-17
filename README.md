# Prometheus Federation And Thanos [Kubernetes Sri Lanka Webinar] | YOUTUBE: https://youtu.be/h369W9SMQfk

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
  --set prometheus.externalLabels.cluster="<CLUSTER-LABEL>" \
  bitnami/prometheus-operator
```
- Then get the sidecar service public ip address:

``` kubectl get svc | grep prometheus-operator-prometheus-thanos ``` or ``` kgs | grep prometheus-operator-prometheus-thanos ```

- Do the same thing for your second data producer cluster.

### Step 2: Install and configure Thanos

- Get the values.yaml file in this repository; do the correct configurations according to that file.

- Install Thanos using the command below:

``` 
helm install thanos bitnami/thanos \
  --values values.yaml
```
- Wait for the deployment to complete and note the DNS name and port number for the Thanos Querier service in the deployment output.

- Follow the instructions according to that output and check it on your browser.

### Step 3: Install Grafana
#### The next step is to install Grafana, also on the same “data aggregator” cluster as Thanos.

- Use the command below, replacing GRAFANA-PASSWORD with a password for the Grafana application:
```
helm install grafana bitnami/grafana \
  --set service.type=LoadBalancer \
  --set admin.password=GRAFANA-PASSWORD
```
- Get the public LB address of grafana using below command:
``` kubectl get svc | grep grafana ``` or ``` kgs | grep grafana ```

- Check grafana on your browser.

### Step 4: Configure Grafana to use Thanos as a data source

- From the Grafana dashboard, click the “Add data source” button.

- On the “Choose data source type” page, select “Prometheus”.
    
- On the “Settings” page, set the URL for the Prometheus server to http://NAME:PORT, where NAME is the DNS name for the Thanos service obtained at the end of Step 2 and PORT is the corresponding service port. Leave all other values at their default.

- Click “Save & Test” to save and test the configuration. If everything is configured correctly, you should see a success message.

### Step 5: Test the system
### To test this system, need to import prometheus node exporter chart, you can use any other relevant thing as well.

- Goto the this URL >> https://grafana.com/grafana/dashboards/11074

- Copy that dashboard ID.

- From the Grafana dashboard, click "+" mark then goto the "Import" sub category.

- Paste that ID in the given field; then load.

- After that you will see how dashboard works in grafana.
