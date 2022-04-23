# View/Create/Delete Pods
## Working with Kubernetes using kubectl
## All Steps needed with comments

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

## Terminology

### 1. kubectl
> The Kubernetes command-line tool, kubectl, allows you to run commands against Kubernetes clusters. You can use kubectl to deploy applications, inspect and manage cluster resources, and view logs. 
> kubectl uses kubernetes API in beackend.

### 2. kubernetes API
> The core of kuberenetes Control Plane is the API server. It is a part of Control plane and provides a single point of communication between various kubernetes components.
> It is a basic HTTP API.
> The API lets users query and manipulate objects, thereby controlling the cluster.

#### Example

`
kubectl get pods -n kube-system
`
Above command is calling kubernetes API as backend to get all pods (Pods are a type of Kubernetes Objects) under namespace named "kube-system"

Alternative way is directly call specific kubernetes API. [Not Recommended]
`
kubectl get --raw /api/v1/namespaces/kube-system/pods
`
It will return a JSON object containing all the pods info.

### 3. Kubernetes Object
> Kubernetes objects are persistent data entities stored by kubernetes. They represent the state of your cluster.
> You can deploy,configure applications, run containers and configure cluster behaviour by creating,modifying and deleting objects.
> Kubernetes Objects are usually represented as YAML format.

#### Example
- pod (Used to run, manage containers)
- service (Used to define microservice)
- etc...

# How Kubernetes Object Work?
## How User can spin off the container in Kubernetes Cluster?

### Step 1 User can request to create the pod in Kubernetes Cluster using Kubernetes API
![Untitled Diagram](https://user-images.githubusercontent.com/42410722/164890895-1b102c6b-4773-4582-a80f-4d82b72a307d.jpg)


### Step 2 Control plane server takes the request and in response, it will create pod and deploy container in any worker node. [It stores the pod (kubernetes object) in Control Plane, So pod represent the State of the Container]
![Untitled Diagram-2](https://user-images.githubusercontent.com/42410722/164890902-893047ad-d054-4496-919f-d4361311be62.jpg)


### Step 3 Worker node can update the status to Control plane using kubernetes API and this info will be stored in the same pod in control plane
![Untitled Diagram-3](https://user-images.githubusercontent.com/42410722/164890913-ad939d70-698e-4760-b8b5-62067af63411.jpg)


### Step 4 User can request to get status of the pod using kubernetes API
![Untitled Diagram-4](https://user-images.githubusercontent.com/42410722/164890915-160f3e58-0538-41fe-8f41-d4c4449cb3d7.jpg)


# Steps to delete pod [Below steps should be performed on Control plane node]

## Step 1 Log in to the Control plane Server

```sh
ssh <username>@<server public IP address or hostname>
```

## Step 2 Get status of all pods

To get all pods in default namespace-

```sh
kubectl get pods
```

To get all pods in all namespaces-

```sh
kubectl get pods --all-namespaces
```

## Step 3 Delete a pod

Lets say a pod named as web-frontend is Crashed/Error 
To delete this-

```sh
kubectl delete pod web-frontend
```

# Steps to create pod [Below steps should be performed on Control plane node]

## Step 1 Log in to the Control plane Server

```sh
ssh <username>@<server public IP address or hostname>
```

## Step 2 Get status of all pods

To get all pods in default namespace-

```sh
kubectl get pods
```

To get all pods in all namespaces-

```sh
kubectl get pods --all-namespaces
```

## Step 3 Create pod

Lets say we want to create web-frontend pod

### Step 3.1 create web-frontend.yml file

```sh
vi web-frontend.yml
```

Content-

```sh
apiVersion: v1
kind: pod
metadata:
  name: web-frontend
spec:
  containers:
  - name: nginx
    image: nginx
```

Save it using ESC + :wq

Here, in a pod, we can have multiple containers but it is not RECOMMENDED.
Also, here, we have a container name nginx and in the image, we are using nginx image which is a web server creation application image.

### Step 3.2 create web-frontend pod

```sh
kubectl create -f web-frontend.yml
```

### Step 4 Check status

```sh
kubectl get pod web-frontend
```

**Happy Coding!**

