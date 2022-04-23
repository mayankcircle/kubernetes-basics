# Running Containers with Kubernetes
## Working with Kubernetes using kubectl
## All Steps needed with comments

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

## Terminology

### 1. pod
> A pod (Kubernetes object) is a group of one or more containers with shared storage and network resources and a specification for how to run the containers.
> If we have multiple containers, always create multiple pods [RECOMMENDED]

#### pod specification basics

```sh
apiVersion: v1
kind: pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx
```

- apiVersion is v1 which is kubernetes API version.
- kind is pod.
- metadata.name represents the name of the pod.
- spec.containers has a list of one or more containers.
- spec.containers[].name is name of the container
- spec.containers[].image is software image of the container.

# Running Containers with kubernetes
# Create the pod in the kubernetes cluster (Below steps should be performed on Control plane)
## Step 1.1 Log in to the Control plane Server

```sh
ssh <username>@<server public IP address or hostname>
```

## Step 1.2 Get status of all pods

To get all pods in default namespace-

```sh
kubectl get pods
```

To get all pods in all namespaces-

```sh
kubectl get pods --all-namespaces
```

## Step 2 Create a pod

We are going to create two pods-
1. nginx 
2. redis

After creation, it will be deployed automatically in kubernetes cluster.

```sh
vi nginx-pod.yml
```

Content-

```sh
apiVersion: v1
kind: pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

As we know, nginx is a web server that will listen to port 80. So, we expose here port 80 for our application.

```sh
kubectl create -f nginx-pod.yml
```

Similarly, we do the same for other pod.

```sh
vi redis-pod.yml
```

Content-

```sh
apiVersion: v1
kind: pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis
```

```sh
kubectl create -f redis-pod.yml
```

## Step 3 Verify the pod

We need IP ADDRESS of our pod, so we use below command-

```sh
kubectl get pods -o wide
```

It will return all info of pods such as IP ADDRESS, where this is running etc.
In our case, each pod represents a Container.

## Step 4 Communicate with the nginx pod

```sh
curl <nginx pod IP ADDRESS>
```

By this, we can see nginx response as HTML in our terminal. It is a web server.

## Step 5 Check nginx pod Logs

```sh
kubectl logs nginx
```

Here, we can see our previous curl request in last line of the log.

**Happy Coding!**

