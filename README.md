# Setting Up a Kubernetes Cluster
## All Steps needed with comments

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

## Prerequisites

- 3 nodes (1 will function as the Control plane Server and rest 2 will serve as Worker Node)
- We use the same environment across all nodes - Dristribution: Ubantu 20.04 Focal Fossa LTS


## Step 1 Install Packages in all nodes (Conntrol Plane + worker nodes)

### Step 1.1 Open terminal of any node (SSH Login to the server)

```sh
ssh <username>@<server public IP address or hostname>
```

### Step 1.2 Make configuration files before installation 
### Step 1.2.1 Enable kernel modules and configure it so that it must be started after the system restart automatically
It creates the modules-load.d file called containerd.conf
Also we enable the overlay and br_netfilter module so it will be automatically up after the Restart.

```sh
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
> overlay
> br_netfilter
> EOF
```

### Step 1.2.2 Enable Modules right now (no need to wait for Restart)

```sh
sudo modprobe overlay
sudo modprobe br_netfilter
```

### Step 1.2.3 System level settings for container networking
It is to make sure that container networking will work as expected.
It creates the sysctl.d file called 99-kubernetes-cri.conf

```sh
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
> net.bridge.bridge-nf-call-iptables = 1
> net.ipv4.ip_forward = 1
> net.bridge.bridge-nf-call-ip6tables = 1
> EOF
```

### Step 1.2.4 Load System level settings for container networking immediately (No need to wait for Restart)

```sh
sudo sysctl --system
```

### Step 1.3 Install containerd

```sh
sudo apt-get update && sudo apt-get install -y containerd
```

### Step 1.4 Create configuration file for containerd

It will create first default configuration file under /etc/containerd directory and then save it to the config.toml

```sh
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml
```

### Step 1.5 Restart containerd to make sure that our config is being used

```sh
sudo systemctl restart containerd
```

### Step 1.6 Check status of the containerd
It must be in ACTIVE state.

```sh
sudo systemctl status containerd
```

press ctrl+c to come out of the message.

### Step 1.7 Installing and configuring Kubernetes Packages 
In order to kubernetes to run, we need to disable the swaps first.

### Step 1.7.1 Disable the swap

```sh
sudo swapoff -a
```

Also remove any entries related to swap in fstab file (if it exists)

```sh
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

### Step 1.7.2 Install some required packages

```sh
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
```

### Step 1.7.3 Add GPG key from Kubernetes Repository

It will download and add GPG key

```sh
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

### Step 1.7.4 Setup actual kubernetes Repository Entry

```sh
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
> deb https://apt.kubernetes.io/ kubernetes-xenial main
> EOF
```

### Step 1.7.5 Update Package Listings with the data of new kubernetes repository

```sh
sudo apt-get update
```

### Step 1.7.6 Install Kubernetes Packages

We are going to install kubelet, kubeadm and kubectl
[Note - We always use same version of these components everywhere. Here we are using 1.23.0-00]

```sh
sudo apt-get install -y kubelet=1.23.0-00 kubeadm=1.23.0-00 kubectl=1.23.0-00
```

### Step 1.7.7 Disable automatic updates on kubernetes packages

```sh
sudo apt-mark hold kubelet kubeadm kubectl
```

Now repeat all steps of Step1 in all nodes (Worker Node + Control Plane)

## Step 2 Initialize the Cluster (Only perform this in Control Plane Server)

### Step 2.1 Initialize the Kubernetes Cluster on Control Plane using kubeadm

```sh
sudo kubeadm init --pod-network-cidr 192.168.0.0/16 --kubernetes-version 1.23.0
```

### Step 2.2 Set kubectl access

Below commands might have printed as messages in the terminal as output of Step 2.1 command.
You need to just copy paste the same.

```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### Step 2.3 Test Access to the Cluster

```sh
kubectl get nodes
```

We may get only one node in the cluster that is Control plane and the status will be "NotReady", it is beacuse we have not set the networking yet.

## Step 3 Install Calico Network Add-on (Only perform this in Control Plane Server)

### Step 3.1 Install Calico Networking

```sh
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

### Step 3.2 Get status of Control Plane Node

```sh
kubectl get nodes
```

Calico Network add on may take some time in background.
By this time, we should get status as "READY". [Try this multiple times]

## Step 4 Join the Worker Node to the Cluster

### Step 4.1 In control plane node, create the token and copy the kubeadm join command

```sh
kubeadm token create --print-join-command
```

Copy the kubeadm join command.

### Step 4.2 In both worker nodes, paste the kubeadm join command

paste the kubeadm join command which you got in step 4.1

### Step 4.3 Get status of cluster in Control Plane Node

```sh
kubectl get nodes
```

It may take some time to be "READY" for all worker nodes.

**Happy Coding!**

