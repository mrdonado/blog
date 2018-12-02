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
- Nodes: workers that run applications. They have:
  - A Kubelet: an agent responsible for communication between the node and the Kubernetes Master (communicating via the Kubernetes API). It manages the pods and the containers running on a machine.
  - A container runtime, such as docker or rkt, responsible for pulling container images, unpacking the container and running an application.

The minimum of nodes in production is 3.

Minikube is a lightweith Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node.

A deployment configuration instructs Kubernetes how to create and update instances of an application. The Kubernetes Deployment Controller makes sure that the instances are up an running. In case a node goes down, the KDC will start the required containers in the remaining working nodes (self-healing mechanism).

The command `kubectl run` creates a new deployment. E.g.: `kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080`.

Kubectl is the Kubernetes CLI, that uses the Kubernetes API in order to interact with the cluster. 

Pods are in an private, isolated network. Pods are visible from other pods and services within the same cluster, but not outside. A pod is an abstraction that represents a group of one or more containers and some shared resources for those containers, such as volumes, networking and container specific information (image version or ports).

A service defines a logical set of pods and a policy by which to access them. It is defined by yaml or json. Services allow applications to receive traffic. You can define the following `type` of services:

- ClusterIP (default): it makes the service only reachable within the cluster.
- NodePort: it exposes the service on the same port of each selected node in the cluster using NAT, making thus the service accessible from outide the cluster via `<NodeIP>:<NodePort>`.
- LoadBalancer
- ExternalName: it exposes the service using an arbitrary name via `externalName` by returning a CNAME record. `kube-dns` v1.7 or higher is required.

A service routes traffic across a set of Pods. They allow pods to die and replicate without impacting the application. Kubernetes Services handle discovery and routing. In order to match a set of pods, services use labels and selectors.

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

`kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml`
`kubectl proxy`
`kubectl create serviceaccount cluster-admin-dashboard-sa`
`kubectl create clusterrolebinding cluster-admin-dashboard-sa \\n  --clusterrole=cluster-admin \\n  --serviceaccount=default:cluster-admin-dashboard-sa`

### Get secret to connect to the dashboard

`kubectl get secret | grep cluster-admin-dashboard-sa`
`kubectl describe secret cluster-admin-dashboard-sa-token-sblqz`

### Run a new image

`kubectl run nginx --image=nginx:1.10.0`

### Get information

`kubectl version`
`kubectl cluster-info`
`kubectl get [all|nodes|pods|deployments|services|replicasets]`
`kubectl describe pods nginx-68c5b54745-hxn8r`
`kubectl logs nginx-68c5b54745-hxn8r -f`

### Expose a deployment

`kubectl expose deployments nginx --port 80 --type LoadBalancer`

### Forward ports

`kubectl port-forward nginx-68c5b54745-hxn8r 10080:80`

### Label

`kubectl label pod $POD_NAME app=v1` add the label `app=v1` to the pod `$POD_NAME`

### Delete

`kubectl delete service -l run=some-app` remove services labeled with `run=some-app`

### Scale

`kubectl scale deployments/kubernetes-bootcamp --replicas=4`

### Update/Rollback and undo

`kubecel set image deployments/your-app=you/your-app:v2`
`kubectl rollout status deployments/your-app`
`kubectl rollout undo deployments/your-app`

### Execute commands on a container in a pod

`kubectl exec nginx-68c5b54745-hxn8r --stdin --tty /bin/sh` Connect to a container's console
