---
layout: post
title:  "Microservices and Kubernetes"
date:   2018-09-10
category: command-line
backgrounds:
    - https://blog.jdonado.com/assets/images/backgrounds/matrix.jpg
thumb: https://blog.jdonado.com/assets/images/terminal.jpg
tags: linux kubernetes docker microservices architecture
---

# Microservices

[Some link]({% link _posts/2017-05-28-vim-magic.markdown %})

{% highlight bash %}
{% endhighlight %}

## Key Issues

- 12 factor https://12factor.net/
- Configuration Repository
- Blue/Green deployments
- Monitoring & Tracing
- Architecture (auto)documentation
- Authentication
- Gateway
  - Traefik

# Kubernetes

Resources

- Tutorials https://kubernetes.io/docs/tutorials/

## Overview

Types of [resources in Kubernetes](https://kubernetes.io/docs/tutorials/kubernetes-basics/create-cluster/cluster-intro/)

- Master: it coordinates all activities in the cluster (scheduling, apps maintenance, scaling, updating...).
- Nodes: workers that run applications. They have a Kubelet (an agent managing the node and communicating with the master via the Kubernetes API). They have a container engine, such as docker or rkt.

The minimum of nodes in production is 3.

Minikube is a lightweith Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node.

A deployment configuration instructs Kubernetes how to create and update instances of an application. The Kubernetes Deployment Controller makes sure that the instances are up an running. In case a node goes down, the KDC will start the required containers in the remaining working nodes (self-healing mechanism).

The command `kubectl run` creates a new deployment. E.g.: `kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080`.

Kubectl is the Kubernetes CLI, that uses the Kubernetes API in order to interact with the cluster. 

Pods in an private, isolated network. Pods are visible from other pods and services within the same cluster, but not outside. 

The command `kubectl proxy` creates a proxy that forwards communications into the cluster-wide private network.

Now save the pod name in an environment variable and access it through the proxy:

```
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')

curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/
```

## Commands

### Minikube

`minikube version`
`minikube start`


### Apply dashboard

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
kubectl proxy
kubectl create serviceaccount cluster-admin-dashboard-sa
kubectl create clusterrolebinding cluster-admin-dashboard-sa \\n  --clusterrole=cluster-admin \\n  --serviceaccount=default:cluster-admin-dashboard-sa

### Get secret to connect to the dashboard

kubectl get secret | grep cluster-admin-dashboard-sa
kubectl describe secret cluster-admin-dashboard-sa-token-sblqz

### Run a new image

kubectl run nginx --image=nginx:1.10.0

### Get information

kubectl version
kubectl cluster-info
kubectl get nodes
kubectl get all
kubectl get pods
kubectl get deployments
kubectl get services
kubectl get replicasets
kubectl describe pods nginx-68c5b54745-hxn8r
kubectl logs nginx-68c5b54745-hxn8r -f

### Expose a deployment

kubectl expose deployments nginx --port 80 --type LoadBalancer

### Forward ports

kubectl port-forward nginx-68c5b54745-hxn8r 10080:80

### Connect to a container's console

kubectl exec nginx-68c5b54745-hxn8r --stdin --tty /bin/sh
