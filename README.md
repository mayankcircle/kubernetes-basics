# kubernetes-basics
This is having all details of basic experiments around Kubernetes.

1. Setting up the kubernetes Cluster - [Doc](setting-up-kubernetes-cluster.md)
2. Creating/Deleting Pod - [Doc](create-delete-pods.md)
3. Running Pods in Kubernetes Cluster - [Doc](running-pods-in-cluster.md)


[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

## Terminology

### 1. Container
> A container is a standard unit of a software that packages up the code and all its dependencies so that application runs quickly and reliably from one computing environment to another.

#### Advantages of Container
- Portability - Container let you run software easily in a variety of environment
- Consistency - Container helps software run in the same way no matter which environment you are running in.
- Low Overhead- Container takes  less resources than virtual machines.

### 2. Container Runtime
> A container Runtime is the software used to run containers on the machine. 
> Example: containerd

### 3. Kubernetes
> Kubernetes, also known as K8s, is an open-source system for automating deployment, scaling, and management of containerized applications. 

#### Functionality of Kubernetes
- Kubernetes helps you deploy containers across a pool of compute resources such as servers.
- Kubernetes can easily manage replicas of your applications.
- Kubernetes makes it easy to SCALE UP or DOWN. (can add more replicas in a server or multiple server and can reduce replicas as well)

Also Kubernetes can do Networking (can control/manage container's network communications), Security (to build more secure app) and Configuration management (Manage application config and pass data to the containers)

### 4. Kubernetes Cluster
> A kubernetes Cluster is a collection of Worker machines that run containers.

#### Basic Architecture of Kubernetes Cluster
##### Control Plane Server-
- This is one of the machine (sometimes multiple machines) that run a collection of the services that control the cluster/Monitor the state of the cluster. [Think this as a Master Node]
- Users Interact with the cluster using the control plane.

##### Worker Node-
- [Also known as a node] A machine the runs actual containers within the cluster.
- It runs and manages containers on the node.
- It monitors the state of the containers on the node and reports the status back to the control plane.

#### Note-
- This Control plane consists of multiple individual components. We can also run multiple instances of each component for high availability. [We can have multiple control plane servers]
- Since each worker node is reponsible for running the container. It must have container Runtime such as containerd.

### 4. Kubelet
> It is a component used to manage Kubernetes activity on the node (Worker Node).

### 5. Kubeadm
> It is a tool that will simplify the process of setting up our kubernetes cluster.

# Setting Up Kubernetes Cluster
![Kubernets Cluster Creation-4](https://user-images.githubusercontent.com/42410722/164884761-f5068288-d76f-4140-b337-3c083d683eda.jpg)

