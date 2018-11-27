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

## Configuration

- Repository

## Gateway

- Traefik

# Kubernetes

## Commands

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

kubectl get all
kubectl get pods
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
