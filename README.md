# Monitoring a Kubernetes Cluster with Grafana and Prometheus - tutorial 

## Description
This project aims to provide a step-by-step guide for setting up and monitoring a Kubernetes cluster using Grafana and Prometheus. The combination of Grafana and Prometheus offers a powerful solution for visualizing and analyzing cluster metrics, allowing you to gain valuable insights into the health and performance of your cluster.

## Creation of a local  Kubernetes cluster using Kind
Install Kind in order to deploy your local Kubernetes cluster, better in a Linux box. Download and install Kind from the official repository: https://github.com/kubernetes-sigs/kind . Make sure Kind is added to your system's PATH variable. 

## Create a Kubernetes cluster using Kind:
Open a terminal or command prompt and type 

```
# For AMD64 / x86_64
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
# For ARM64
[ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-arm64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

```
kind create cluster --name my-cluster   or

go install sigs.k8s.io/kind@v0.20.0 && kind create cluster --name my-cluster
```

This will create a new Kubernetes cluster with the name "my-cluster". You can replace "my-cluster" with a name of your choice.
Create the "monitoring" and "kubernetes-dashboard" namespaces:

Open a terminal or command prompt.

```
kubectl create namespace monitoring 
kubectl create namespace kubernetes-dashboard
```

After executing the command, the "monitoring" namespace will be created in your Kind cluster with the name "my-cluster".
Verify the namespace creation:

```
kubectl get namespaces --cluster my-cluster or

kubectl get ns
```

You should see the "monitoring" namespace listed in the output, indicating that it was successfully created.
That's it! You have successfully created a namespace named "monitoring" in your Kind cluster. You can now use this namespace to deploy your monitoring components such as Grafana and Prometheus.

## Configuring the Kubernetes dashboard. 
To configure the Kubernetes Dashboard in your cluster, you need to create a ServiceAccount and a ClusterRoleBinding. You can have in this project using the folder kubernetes-dashboard. Apply then the following snippets: 

```
kubectl apply -f service_account.yaml
```
Same for the ClusterRoleBinfing and the secret for obtaining the token

```
kubectl apply -f cluster_binding.yaml
kubectl apply -f secret.yaml
```
A lot of support for installing and configuring Kubernetes dashboards is provided in the official site, here : https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

After the steps above, access the Kubernetes dashboard obtaining the token :

```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')

```
and copy the token generated. Starting the dashboard can be done using the proxy: 
```
kubectl proxy
```
Open your web browser and navigate to:

```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/.

```
Select the "Token" option on the login page.
Paste the token copied earlier into the "Token" field.
Click the "Sign In" button to access the Kubernetes Dashboard.

## Experiment with a local cluster in a Linux box using Minikube
All the steps outlined above can be avoided if we decide to install the Kubernetes cluster using Minikube. Minikube is another viable solution for install and experimenting with Kuberentes clusters. More info at : https://kubernetes.io/docs/tutorials/kubernetes-basics/create-cluster/cluster-intro/ .  After  the installation and creation of the cluster, the dashboard is accessible via the following commands: 

```
minikube dashboard
```


## Install Helm , the package manager for Kubernetes
Helm is a package manager for Kubernetes that simplifies the deployment and management of applications on a Kubernetes cluster. It provides a higher-level abstraction and streamlines the process of packaging, versioning, and releasing applications. For install Helm, go to following link https://helm.sh/docs/intro/install/ you type the commands: 

    a) Download your desired version
    b) Unpack it (tar -zxvf helm-v3.0.0-linux-amd64.tar.gz)
    c) Find the helm binary in the unpacked directory, and move it to its desired destination (mv linux-amd64/helm /usr/local/bin/helm)

## Install Prometheus and Grafana
Prometheus is a free software application used for event monitoring and alerting.It records metrics in a time series database (allowing for high dimensionality) built using an HTTP pull model, with flexible queries and real-time alerting. In this section we will install Prometheus and Grafana and provide actionable examples of their usage. 

# Configuring Prometheus with Helm. 

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring

kubectl --namespace monitoring get pods -l "release=prometheus"

kubectl get pods -n monitoring

kubectl get svc -n monitoring

kubectl port-forward svc/prometheus-kube-prometheus-prometheus -n monitoring 9090

helm repo update
```

# Play with common queries for measure CPU utilization, memory usage, etc
With only Prometheus installed in your cluster is more or less easy measure some common metrics for the CPU utilization or some other useful performance indicators. Let's play with some easy queries, accessible from the Prometheus dashboard ( serving at 127.0.0.1:9090 in our example. 

### Memory Usage in Percent
```
100 * (1 - ((avg_over_time(node_memory_MemFree_bytes[10m]) + avg_over_time(node_memory_Cached_bytes[10m]) + avg_over_time(node_memory_Buffers_bytes[10m])) / avg_over_time(node_memory_MemTotal_bytes[10m])))
```
<img width="947" alt="memory_usage_percent" src="https://github.com/NicoLiberato/Monitoring-Kubernetes-Grafana-Prometheus/assets/12775912/e50d53c8-e001-47ed-99d7-b88d06c66224">

### Histogram
```
histogram_quantile(1.00, sum(rate(prometheus_http_request_duration_seconds_bucket[5m])) by (handler, le)) * 1e3)
```
<img width="950" alt="histogram" src="https://github.com/NicoLiberato/Monitoring-Kubernetes-Grafana-Prometheus/assets/12775912/8a2e96d8-100e-4d39-920b-475cae156c7b">

## Configure Grafana
```
 helm repo add grafana https://grafana.github.io/helm-charts
 helm install grafana grafana/grafana

 1. Get your 'admin' user password by running:

   kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo


 2. The Grafana server can be accessed via port 80 on the following DNS name from within your cluster:

   grafana.default.svc.cluster.local

   Get the Grafana URL to visit by running these commands in the same shell:
     export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=grafana,app.kubernetes.io  /instance=grafana" -o jsonpath="{.items[0].metadata.name}")
     kubectl --namespace default port-forward $POD_NAME 3000

```
## Adding examples of visualization metrics in Grafana




<img width="947" alt="grafana_example" src="https://github.com/NicoLiberato/Monitoring-Kubernetes-Grafana-Prometheus/assets/12775912/cf8f3379-1b07-4081-8494-dad51032d4ea">





