Monitoring a Kubernetes Cluster with Grafana and Prometheus

## Description
This project aims to provide a step-by-step guide for setting up and monitoring a Kubernetes cluster using Grafana and Prometheus. The combination of Grafana and Prometheus offers a powerful solution for visualizing and analyzing cluster metrics, allowing you to gain valuable insights into the health and performance of your cluster.

## Creation of a local workspace in a Kubernetes cluster
Install Kind in order to deploy your local Kubernetes cluster, better in a Linux box. Download and install Kind from the official repository: https://github.com/kubernetes-sigs/kind . Make sure Kind is added to your system's PATH variable.

## Create a Kubernetes cluster using Kind:
Open a terminal or command prompt and type 

```
kind create cluster --name my-cluster
```

This will create a new Kubernetes cluster with the name "my-cluster". You can replace "my-cluster" with a name of your choice.
Create the "monitoring" namespace:

Open a terminal or command prompt.

```
kubectl create namespace monitoring --cluster my-cluster
```

After executing the command, the "monitoring" namespace will be created in your Kind cluster with the name "my-cluster".
Verify the namespace creation:

```
kubectl get namespaces --cluster my-cluster
```

You should see the "monitoring" namespace listed in the output, indicating that it was successfully created.
That's it! You have successfully created a namespace named "monitoring" in your Kind cluster. You can now use this namespace to deploy your monitoring components such as Grafana and Prometheus.

## Install Helm , the package manager for Kubernetes
Helm is a package manager for Kubernetes that simplifies the deployment and management of applications on a Kubernetes cluster. It provides a higher-level abstraction and streamlines the process of packaging, versioning, and releasing applications. For install Helm, you can follow the steps: 

    a) Download your desired version
    b) Unpack it (tar -zxvf helm-v3.0.0-linux-amd64.tar.gz)
    c) Find the helm binary in the unpacked directory, and move it to its desired destination (mv linux-amd64/helm /usr/local/bin/helm)






## Installation 
This section provides instructions for installing Grafana and Prometheus in your Kubernetes cluster.







