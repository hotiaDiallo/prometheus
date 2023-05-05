
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

We would like to use `Prometheus` to monitor our Kubernetes cluster as well as the applications that are deployed in this cluster, we want to deploy the Prometheus server on this cluster using the `prometheus-community/kube-prometheus-stack` helm chart.
We will name it `prometheus`. 

### Get Helm Repository Info

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

### Install Helm Chart

```
helm install prometheus prometheus-community/kube-prometheus-stack
```

`Note:` Due to a known bug you might need to patch the Prometheus node exporter DaemonSet using below command.

```
kubectl patch ds prometheus-prometheus-node-exporter --type "json" -p '[{"op": "remove", "path" : "/spec/template/spec/containers/0/volumeMounts/2/mountPropagation"}]'
```

There are 3 deployments:

- `prometheus-grafana` - This is the grafana instance that gets deployed with the helm chart.

- `prometheus-kube-prometheus-operator` - Responsible for deploying & managing the prometheus instance.

- `prometheus-kube-state-metrics` - Collects cluster level metrics (pods, deployments, etc).


There are two statefulsets that have been deployed by the helm chart:


- `prometheus-prometheus-kube-prometheus-prometheus` - This is the prometheus instance.


- `alertmanager-prometheus-kube-prometheus-alertmanager` - This is the alertmanager instance

`Note`: The prometheus instance is using a `ClusterIP` service called `prometheus-kube-prometheus-prometheus`, which means it is only accessible within the cluster, and we can’t access the UI by default.

To overcome that, we are going to update `prometheus-kube-prometheus-prometheus` service to change it to `NodePort` service. 
