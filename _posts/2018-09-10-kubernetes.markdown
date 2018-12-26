---
layout: post
title:  "Kubernetes: beyond docker compose"
date:   2018-09-10
category: cloud
backgrounds:
    - https://blog.jdonado.com/assets/images/backgrounds/matrix.jpg
thumb: https://blog.jdonado.com/assets/images/terminal.jpg
tags: kubernetes docker docker-compose linux
---

# Beyond docker compose

Right after I started using docker to containerize my applications, I used to include a `docker-compose.yml` file together with them (sometimes in a separate repository) in order to illustrate how a local deployment could work. With [docker-compose](https://docs.docker.com/compose/) you can:

- Define which services and applications are necessary to run a specific solution
- Detail how they may interact with each other
- Specify in which order they should start
- Describe the network in which the containers are going to be deployed
- Setup the environment for everything to work in harmony

After a while I got used to the simplicity and usefulness of docker compose. However, deploying services with docker compose like this (that is: without [swarm mode](https://docs.docker.com/engine/swarm/)) had an important limitation: **you could only deploy to a single machine**.

If you have more advanced deployment needs, e.g.:

- Deployment of services on a cluster with various nodes
- Need of a scaling strategy
- Coordinate updates with zero-downtime deployment

then you need a container orchestrator. There are a number of solutions for orchestration of containers in the cloud: Docker Swarm, Kubernetes, Apache Mesos, CloudFoundry's Diego...

Depending on your needs, the right tool might vary. For me, Kubernetes seemed like a very good compromise between features and ease of use. Some of the reasons why I think it's a good choice are:

- It's perfectly integrated with docker. If you're already using docker containers, then you're halfway there
- It is open source. That means (among other things) that you're not attached to a specific provider. There are currently a number of providers of Kubernetes as a service in the cloud and you're always free to deploy Kubernetes clusters yourself on whichever your cloud infrastructure is
- The [documentation](https://kubernetes.io/docs/home/?path=users&persona=app-developer&level=foundational) is very good
- It's currently [by far the most popular](https://thenewstack.io/data-says-kubernetes-deployment-patterns/) container orchestration technology. If you have any problem, there's a good chance that you'll find help on [StackOverflow](https://stackoverflow.com/questions/tagged/kubernetes)
- You can install minikube on your machine and test your complete solution even before it reaches the cloud. If something goes wrong, you can try to find out what's happening directly on your machine without having to wait for things to upload and deploy to the cloud

For me this last point is a game changer as compared to other cloud technologies. The ability to try and run everything locally makes everything easier. Forget about small changes on a `yml` file and trigger the CI pipelines to (pray and) see if a deployment works. Instead of that just make sure that your configuration is up and running locally on your minikube and chances are everything will work out just like that on the cloud.

## Learn Kubernetes

If you want to learn Kubernetes, the [tutorials](https://kubernetes.io/docs/tutorials/) section of the official Kubernetes website is a great starting point. I myself followed it and then I completed a couple of the [online training](https://kubernetes.io/docs/tutorials/online-training/overview/) courses that they link to.

What follows are the notes I took while learning about it.

# Kubernetes: a quick overview for developers

Kubernetes is an open source container orchestrator created by Google and now maintained by the [Cloud Native Computing Foundation](https://en.wikipedia.org/wiki/Linux_Foundation#Cloud_Native_Computing_Foundation).

Every Kubernetes infrastructure has the following components:

- Master: it coordinates all activities in the cluster (scheduling, apps maintenance, scaling, updating...)
- Nodes: workers that run applications. They have:
  - A Kubelet: an agent responsible for communication between the node and the Kubernetes Master (communicating via the Kubernetes API). It manages the pods and the containers running on a machine
  - A container runtime, such as docker or rkt, responsible for pulling container images, unpacking the container and running an application

The minimum of nodes in a production infrastructure is 3.

You can run Kubernetes locally on your machine by installing **minikube**. Minikube is a lightweight Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node.

**Kubectl** is the Kubernetes CLI, that uses the Kubernetes API in order to interact with the cluster. You can use it to connect to your minikube and handle your deployments locally, and you can also connect to your cloud provider and handle your deployments remotely. It is kind of an equivalent to the `docker` command for docker.

In order to deploy an application in Kubernetes, you can define a deployment configuration in a `yml` (preferred) or in a `json` file. A deployment configuration instructs Kubernetes how to create and update instances of an application. The Kubernetes Deployment Controller (KDC) makes sure that the instances are up an running. NOTE: my next blog post will show a simple example of a microservices deployment in kubernetes. Here I will continue defining the basic concepts and commands for future quick reference.

In case a node goes down, the KDC will start the required containers in the remaining working nodes (self-healing mechanism).

The command `kubectl run` creates a new deployment. E.g.: `kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080`.

**Pods** are the smallest deployable units of computing that can be created and managed in Kubernetes. Pods are in an private, isolated network. They are visible from other pods and services within the same cluster, but not from outside. A pod is an abstraction that represents a group of one or more containers and some shared resources for those containers, such as volumes, networking and container specific information (image version or ports).

A **service** defines a logical set of pods and a policy to determine how to access them. It is defined by `yaml` or `json`. Services allow applications to receive traffic. You can define the following types of services (in the `type` field of the `yaml` file):

- **ClusterIP** (default): it makes the service only reachable within the cluster.
- **NodePort**: it exposes the service on the same port of each selected node in the cluster using NAT, making thus the service accessible from outide the cluster via `<NodeIP>:<NodePort>`.
- **LoadBalancer**
- **ExternalName**: it exposes the service using an arbitrary name via `externalName` by returning a CNAME record. `kube-dns` v1.7 or higher is required.

A service routes traffic across a set of Pods. They allow pods to die and replicate without impacting the application. Kubernetes Services handle discovery and routing. In order to match a set of pods, services use labels and selectors.

## Kubectl quick reference

```sh
# Create a proxy that forwards communications into the cluster-wide
# private network.
kubectl proxy

# Save the current pod name in an environment variable
# and access it through the proxy
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')

# Test the pod access via proxy
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/

# Minikube
minikube version
minikube start

# Apply dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
kubectl proxy
kubectl create serviceaccount cluster-admin-dashboard-sa
kubectl create clusterrolebinding cluster-admin-dashboard-sa \\n  --clusterrole=cluster-admin \\n  --serviceaccount=default:cluster-admin-dashboard-sa

# Get secret to connect to the dashboard
kubectl get secret | grep cluster-admin-dashboard-sa
kubectl describe secret cluster-admin-dashboard-sa-token-sblqz

# Run a new image
kubectl run nginx --image=nginx:1.10.0

# Get information
kubectl version
kubectl cluster-info
kubectl get [all|nodes|pods|deployments|services|replicasets]
kubectl describe pods nginx-68c5b54745-hxn8r
kubectl logs nginx-68c5b54745-hxn8r -f

# Expose a deployment
kubectl expose deployments nginx --port 80 --type LoadBalancer

# Forward ports
kubectl port-forward nginx-68c5b54745-hxn8r 10080:80

# Label
kubectl label pod $POD_NAME app=v1 # Add the label `app=v1` to the pod `$POD_NAME`

# Delete
kubectl delete service -l run=some-app # Remove services labeled with `run=some-app`

# Scale
kubectl scale deployments/kubernetes-bootcamp --replicas=4

# Update/Rollback and undo
kubecel set image deployments/your-app=you/your-app:v2
kubectl rollout status deployments/your-app
kubectl rollout undo deployments/your-app

# Execute commands on a container in a pod
kubectl exec nginx-68c5b54745-hxn8r --stdin --tty /bin/sh

```
