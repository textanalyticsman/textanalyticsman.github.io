---
date: '2023-07-23 17:24:00'
tags: [kubernetes, kubeadm, cluster, OCI, Oracle]
layout: post
cover: /pictures/2023-07-23-ConfigurationDocument/cover.png
categories: kubernetes_install_cluster
title: Deploying a Kubernetes cluster on Oracle Cloud Infrastructure
author: textanalyticsman
---
- [Introduction](#introduction)
- [Architecture](#architecture)
- [Provisioning the infrastructure on OCI](#provisioning-the-infrastructure-on-oci)
  - [Creating a compartment.](#creating-a-compartment)
  - [Creating a virtual cloud network (VCN)](#creating-a-virtual-cloud-network-vcn)
  - [Creating the virtual machines](#creating-the-virtual-machines)
    - [Creating the control-plane](#creating-the-control-plane)
- [Installing kubeadm, kebeles and kubectl (ALL NODES)](#installing-kubeadm-kebeles-and-kubectl-all-nodes)
- [Installing a Container Runtime Interface (CRI) (ALL NODES)](#installing-a-container-runtime-interface-cri-all-nodes)
    - [Forwarding IPv4 and letting iptables see bridged traffic](#forwarding-ipv4-and-letting-iptables-see-bridged-traffic)
    - [Installing containerd](#installing-containerd)
    - [Installing runc](#installing-runc)
    - [Installing CNI plugins](#installing-cni-plugins)
    - [Creating /etc/containerd/config.toml](#creating-etccontainerdconfigtoml)
    - [Configuring the systemd cgroup driver](#configuring-the-systemd-cgroup-driver)
  - [Initializing your control-plane node (control-plane node)](#initializing-your-control-plane-node-control-plane-node)
  - [Deploying a pod network to the cluster (control-plane node)](#deploying-a-pod-network-to-the-cluster-control-plane-node)
  - [Preparing the cluster to add worker nodes.](#preparing-the-cluster-to-add-worker-nodes)
    - [Configuring the VCN on OCI to allow the traffic on port 6443](#configuring-the-vcn-on-oci-to-allow-the-traffic-on-port-6443)
    - [Configuring iptables to allow traffic throughout port 6443 (control-plane node)](#configuring-iptables-to-allow-traffic-throughout-port-6443-control-plane-node)
- [Adding worker nodes to the Kubernetes cluster.](#adding-worker-nodes-to-the-kubernetes-cluster)
  - [Checking the nodes and pods with kubectl](#checking-the-nodes-and-pods-with-kubectl)

Introduction
============

The goal of this guide is to show how to use kubeadm to create a
Kubernetes cluster on three machines provisioned on Oracle Cloud
Infrastructure (OCI). Thus, the following sections will describe the
topology used, the configuration needed on OCI and all the steps
performed to deploy a Kubernetes cluster on top of these machines.

Architecture
============

The architecture is quite simple and is depicted by the diagram below.

<img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image1.png" alt="A diagram of a diagram Description automatically generated" style="width:5.84375in;height:5.23958in" />
<br/><br/>
From above diagram we will have three virtual machines one for the
control plane and two for the worker nodes.
<br/><br/>

Provisioning the infrastructure on OCI
======================================

Creating a compartment.
-----------------------

Follow these steps.

1.  **Click on the hamburger menu and then click on “Identity & Security”**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image2.png" alt="A screenshot of a search box Description automatically generated" style="width:1.92375in;height:2.9125in" />

2.  **Click on “Create Compartment”**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image3.png" alt="A screenshot of a computer Description automatically generated" style="width:6.17553in;height:1.72515in" />

3.  **Fill the form and click on “Create Compartment”**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image4.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:4.70417in" />

Creating a virtual cloud network (VCN)
--------------------------------------

1.  **Click on the hamburger menu and then click on “Networking &gt;
    Virtual Cloud Networks”.**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image5.png" alt="A screenshot of a computer Description automatically generated" style="width:5.45881in;height:2.94192in" />

2.  **Select the compartment created before.**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image6.png" alt="A screenshot of a computer Description automatically generated" style="width:3.14194in;height:4.72541in" />

3.  **Click on “Start VCN Wizard”.**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image7.png" alt="A screenshot of a computer Description automatically generated" style="width:5.89218in;height:3.18361in" />

4.  **Select the one with Internet connectivity.**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image8.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:3.05764in" />

5.  **Provide a name, a compartment and then click on “Next”**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image9.png" alt="A screenshot of a computer Description automatically generated" style="width:5.68383in;height:4.00868in" />

6.  **Check the configuration and then click on “Create”**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image10.png" alt="A screenshot of a computer Description automatically generated" style="width:6.20887in;height:4.68374in" />

7.  **If everything goes well, you will see this.**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image11.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:3.27431in" />

Creating the virtual machines
-----------------------------

### Creating the control-plane

1.  **Click on the hamburger menu and then click on “Compute &gt;
    Instances”.**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image12.png" alt="A screenshot of a computer Description automatically generated" style="width:3.94201in;height:3.01693in" />

2.  **Click on “Instances” while choosing the right Compartment.**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image13.png" alt="A screenshot of a computer Description automatically generated" style="width:3.06693in;height:5.93385in" />

3.  **Click on “Create Instance”**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image14.png" alt="A screenshot of a computer Description automatically generated" style="width:3.892in;height:3.25862in" />

4.  **Below, we have the options used for the control-plane.**

    **Name and Compartment**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image15.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:1.60694in" />
    
    **Image and shape**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image16.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.78264in" />

    **Networking**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image17.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:3.20764in" />

    **Don’t forget to save your private key.**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image18.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.38333in" />

    **Now you should click on “Create”.**

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image19.png" alt="A screenshot of a computer Description automatically generated" style="width:4.64207in;height:2.88358in" />

The process used to create two worker nodes is pretty much the same so,
the table below summarizes it.

<table>
<thead>
<tr class="header">
<th><strong>Instance name</strong></th>
<th><strong>Compartment</strong></th>
<th><strong>Image</strong></th>
<th><strong>Shape OCPUs</strong></th>
<th><strong>Shape memory</strong></th>
<th><strong>Networking VCN</strong></th>
<th><strong>Networking subnet</strong></th>
<th><strong>Networking public IPv4 address</strong></th>
<th><strong>SSH keys</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Control-plane</td>
<td>cubeClusterCompartment</td>
<td>Canonical Ubuntu 22.04</td>
<td>2</td>
<td>12 GB</td>
<td>kubeclustervcn</td>
<td>public subnet- clustervcn</td>
<td>Yes</td>
<td><p>“Generate a key pair for me”</p>
<p>Save the private key</p></td>
</tr>
<tr class="even">
<td>Worker-01</td>
<td>cubeClusterCompartment</td>
<td>Canonical Ubuntu 22.04</td>
<td>1</td>
<td>6 GB</td>
<td>kubeclustervcn</td>
<td>public subnet- clustervcn</td>
<td>Yes</td>
<td><p>“Generate a key pair for me”</p>
<p>Save the private key</p></td>
</tr>
<tr class="odd">
<td>Worker-02</td>
<td>cubeClusterCompartment</td>
<td>Canonical Ubuntu 22.04</td>
<td>1</td>
<td>6 GB</td>
<td>kubeclustervcn</td>
<td>public subnet- clustervcn</td>
<td>Yes</td>
<td><p>“Generate a key pair for me”</p>
<p>Save the private key</p></td>
</tr>
</tbody>
</table>

Installing kubeadm, kebeles and kubectl (ALL NODES)
===================================================
Source:
<https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/>

We should run these commands on the control-plane node and our worker
nodes.

1.  Update the apt package index and install packages needed to use the
    Kubernetes apt repository.

    ```console
    sudo apt-get update
    sudo apt-get install -y apt-transport-https ca-certificates curl
    ```


    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image20.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.23403in" />

2.  Download the Google Cloud public signing key.

    ```console
    curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image21.png" alt="A screenshot of a computer screen Description automatically generated" style="width:6.5in;height:1.97083in" />

3.  Add the Kubernetes apt repository.

    ```console
    echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image22.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:1.92431in" />

4.  Update apt package index, install kubelet, kubeadm and kubectl, and
    pin their version.

    ```console
        sudo apt-get update
        sudo apt-get install -y kubelet kubeadm kubectl
        sudo apt-mark hold kubelet kubeadm kubectl
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image23.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.65139in" />


Installing a Container Runtime Interface (CRI) (ALL NODES)
==========================================================

In this case we are going to install **containerd**

### Forwarding IPv4 and letting iptables see bridged traffic

Source:
<https://kubernetes.io/docs/setup/production-environment/container-runtimes/>

This is one of the prerequisites to have a CRI in place.

1.  Perform below configuration.

    ```console
        cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
        overlay
        br_netfilter
        EOF
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image24.png" alt="A black screen with white text Description automatically generated" style="width:5.61667in;height:1.55616in" />

    ```console
    sudo modprobe overlay
    sudo modprobe br_netfilter
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image25.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.60903in" />

    \# sysctl params required by setup, params persist across reboots

    ```console
        sudo cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
        net.bridge.bridge-nf-call-iptables  = 1
        net.bridge.bridge-nf-call-ip6tables = 1
        net.ipv4.ip_forward                 = 1
        EOF
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image26.png" alt="A screenshot of a computer screen Description automatically generated" style="width:6.5in;height:2.71319in" />

    \# Apply sysctl params without reboot

    ```console
    sudo sysctl –system
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image27.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.49097in" />

2.  Verify that the br\_netfilter, overlay modules are loaded by running
    the following commands:

    ```console
    lsmod | grep br_netfilter
    lsmod | grep overlay
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image28.png" alt="A screenshot of a computer screen Description automatically generated" style="width:6.5in;height:2.82708in" />

3.  Verify that the net.bridge.bridge-nf-call-iptables,
    net.bridge.bridge-nf-call-ip6tables, and net.ipv4.ip\_forward system
    variables are set to 1 in your sysctl config by running the
    following command:

    ```console
    sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image29.png" alt="A screenshot of a computer screen Description automatically generated" style="width:6.5in;height:2.19167in" />

### Installing containerd

Source:
https://github.com/containerd/containerd/blob/main/docs/getting-started.md

1.  Get the official binary according to the Linux distribution and the
    hardware you are using; in our case.

    ```console
    wget https://github.com/containerd/containerd/releases/download/v1.7.2/containerd-1.7.2-linux-arm64.tar.gz
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image30.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.05486in" />

2.  Unpack the binary

    ```console
    sudo tar Cxzvf /usr/local containerd-1.7.2-linux-arm64.tar.gz
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image31.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:1.95625in" />

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image32.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.38403in" />

3.  Configuring systemd by executing below commands.

    ```console
    wget
    https://raw.githubusercontent.com/containerd/containerd/main/containerd.service
    sudo cp containerd.service /lib/systemd/system/containerd.service
    sudo systemctl daemon-reload
    sudo systemctl enable --now containerd
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image33.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.28403in" />

4.  Check the containerd service

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image34.png" alt="A screenshot of a computer screen Description automatically generated" style="width:6.5in;height:2.24861in" />

### Installing runc

Source:
<https://github.com/containerd/containerd/blob/main/docs/getting-started.md>

Get the proper binary for your Linux distribution and hardware and then
install it by using the commands below.

```console
wget https://github.com/opencontainers/runc/releases/download/v1.1.8/runc.arm64
sudo install -m 755 runc.arm64 /usr/local/sbin/runc
```

<img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image35.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:1.94236in" />

### Installing CNI plugins

Source:
<https://github.com/containerd/containerd/blob/main/docs/getting-started.md>

Get the proper binary for your Linux distribution and hardware and then
install it by using the commands below.

```console
wget https://github.com/containernetworking/plugins/releases/download/v1.3.0/cni-plugins-linux-arm-v1.3.0.tgz
sudo mkdir -p /opt/cni/bin
sudo tar Cxzvf /opt/cni/bin cni-plugins-linux-arm-v1.3.0.tgz
```

<img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image36.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.21042in" />

You will see an outcome similar to this.

<img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image37.png" alt="A screenshot of a computer program Description automatically generated" style="width:6.5in;height:4.57083in" />

### Creating /etc/containerd/config.toml

Source:
<https://github.com/containerd/containerd/blob/main/docs/getting-started.md>

Execute the below commands to create the “config.toml” file with default
values.

```console
sudo mkdir /etc/containerd/
sudo su - 
containerd config default > /etc/containerd/config.toml
```

<img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image38.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.24097in" />

### Configuring the systemd cgroup driver

Source:
<https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd>

To use the systemd cgroup driver in /etc/containerd/config.toml with
runc, follow below instructions.

```console
sudo vi /etc/containerd/config.toml
```

Then set **SystemdCgroup** to true as is shown below.

```console
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  ...
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true  <--------
```

<img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image39.png" alt="A screenshot of a computer program Description automatically generated" style="width:6.5in;height:2.83819in" />

Execute these commands

```console
sudo systemctl restart containerd
sudo systemctl restart kubelet
```

<img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image40.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.35972in" />

Initializing your control-plane node (control-plane node)
---------------------------------------------------------

This is the moment we have been waiting for

1.  Go to control-plane node.

2.  Run this command.

    ```console
    sudo kubeadm init --v=5
    ```

3.  If everything goes well, you will see an outcome like this.

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image41.png" alt="A screenshot of a computer program Description automatically generated" style="width:5.92222in;height:2.475in" />

    Above figure shows that we should deploy a pod network, we will see
    this below and the have a token and a hash to add worker to the
    cluster so, please save this information.

Deploying a pod network to the cluster (control-plane node)
-----------------------------------------------------------

Source: <https://www.golinuxcloud.com/calico-kubernetes/>

Follow below steps.

1.  Get the Calico networking manifest.

    ```console
    curl https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml -O
    ```

2.  Execute below command.

    ```console
    sudo KUBECONFIG=/etc/kubernetes/admin.conf kubectl apply -f calico.yaml
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image42.png" alt="A screen shot of a computer Description automatically generated" style="width:6.5in;height:4.01806in" />

Preparing the cluster to add worker nodes.
------------------------------------------

Let’s follow the steps below.

### Configuring the VCN on OCI to allow the traffic on port 6443

This is important as worker nodes will try to contact the control-plane
node via port 6443.

1.  Go to OCI console.

2.  Click on “Networking &gt; Virtual cloud networks”.

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image43.png" alt="A screenshot of a computer Description automatically generated" style="width:4.72541in;height:2.6669in" />

3.  Click on the VCN the Kubernetes cluster is using.

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image44.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.25903in" />

4.  Click on “Security Lists”.

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image45.png" alt="A screenshot of a computer Description automatically generated" style="width:2.83358in;height:3.18361in" />

5.  Choose the default security list.

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image46.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.28611in" />

6.  Click on “Add Ingress Rules”.

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image47.png" alt="A screenshot of a computer Description automatically generated" style="width:5.91718in;height:2.26686in" />

7.  Fill in the form and click on “Add Ingress Rules”.

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image48.png" alt="A screenshot of a computer Description automatically generated" style="width:6.5in;height:2.56667in" />

### Configuring iptables to allow traffic throughout port 6443 (control-plane node)

This configuration will be gone after rebooting the control-plane node,
which is fine as we need it just once to join the worker nodes to the
cluster.

Execute the below command.

```console
sudo iptables -I INPUT -p tcp -m state --state NEW,ESTABLISHED -m tcp --dport 6443 -m comment --comment "Required for Kubernetes." -j ACCEPT
```

<img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image49.png" style="width:6.5in;height:0.5875in" />

Adding worker nodes to the Kubernetes cluster.
==============================================

Follow the below instructions.

1.  If you lose the toke to join worker nodes, get a new one with below
    command.

    ```console
    sudo kubeadm token create --print-join-command
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image50.png" style="width:6.5in;height:0.44375in" />

2.  Execute the generated command on every worker node; in our case we
    have two nodes.

    ```console
    sudo kubeadm join 10.0.0.228:6443 --token fvnkpq.5nuyu1o3064fryn4 --discovery-token-ca-cert-hash sha256:baffbdb7b6993bdc41384ca46053536e05b04e0359e8b8dac78a22e6a4422358
    ```

1.  If everything goes well, you will see this.

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image51.png" alt="A screenshot of a computer program Description automatically generated" style="width:6.5in;height:1.78472in" />

Checking the nodes and pods with kubectl
----------------------------------------

1.  Go to the control-plane node and become root.

2.  Set this variable.

    ```console
    export KUBECONFIG=/etc/kubernetes/admin.conf
    ```

3.  Execute the command below to check the nodes.

    ```console
    kubectl get nodes -o wide
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image52.png" style="width:6.5in;height:0.51111in" />

1.  Execute the command below to check the pods.

    ```console
    kubectl get pods -A -o wide
    ```

    <img src="../pictures/2023-07-23-ConfigurationDocument/images/media/image53.png" alt="A screenshot of a computer screen Description automatically generated" style="width:6.5in;height:1.55417in" />
