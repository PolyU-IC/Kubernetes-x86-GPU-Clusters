# Kubernetes-x86-GPU-Clusters
This repository is a guildeline for setting up a x86 Nvidia GPU Cluster using Kubernetes. A **"Single Master Multi Slaves** topology is implemented and tested with the following hardware specifications.

1. Intel Core i7-4790 CPU 3.6GHz x8
2. GeForce GTX 1650/PCIe/SSE2
3. Memory 15.6 Gib
4. Ubuntu 16.04 LTS 64-bit
5. Storage 516.4 GB

**NVIDIA GTX 1650**
---------------------------
The GeForce 1650 is built with the breakthrough graphics performance of the award-wining NVIDIA Turing architecture. This GPU will be the cornerstone of the deeplearning cluster built by kubernetes.

![image](https://github.com/vincent51689453/Kubernetes-x86-GPU-Clusters/blob/master/Github_Image/nvidia_logo.jpg)

**GPU Cluster Setup**
---------------------------
### A. Master Node
The actual configuration steps I performed are written in ***Installation_master***, some steps are not essential. You may refer to it if you have any difficulties. I will briefly explain the essential steps you have to perform while setting up master nodes.

1) Unplug your GPU card from your motherboard

2) Install Ubuntu 16.04 to your harddisk

3) Install Nvidia driver 430
```
$ sudo add-apt-repository ppa:graphics-drivers/ppa
$ sudo apt update
$ sudo apt install ubuntu-drivers-common
$ sudo apt install nvidia-430

or ./tools/install_gtx1650_430.sh
```

4) Shutdown and plug GTX1650 back to the motherboard

5) Power it up and check the nvidia driver
```
$ nvidia-smi
```

6) Update and upgrade ubuntu
```
$ sudo apt-get update && sudo apt-get upgrade
```

7) Install docker
```
$ sudo apt -y update
$ sudo apt -y install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
$ sudo apt remove docker docker-engine docker.io containerd runc
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu    $(lsb_release -cs) stable"
$ sudo apt update
$ sudo apt -y install docker-ce docker-ce-cli containerd.io
$ sudo usermod -aG docker vincent
$ newgrp docker
$ docker version
```
8) Install kubernetes k8s
```
$ cd tools/
$ sudo chmod +x install_k8s.sh
$ sudo ./install.sh
```

9) Permanently disable swap
```
$ sudo swapoff -a
$ sudo gedit /etc/fstab
Comment the lines with "swap"
$ sudo reboot
$ sudo swapoff -a
```

![image](https://github.com/vincent51689453/Kubernetes-x86-GPU-Clusters/blob/master/Github_Image/swap_disable.png)

9) Initialize cluster
```
   $ sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```
In case you want to reset,
```   
   $ sudo kubeadm reset
```   
![image](https://github.com/vincent51689453/Kubernetes-x86-GPU-Clusters/blob/master/Github_Image/cluster_init.png)

10) Follow the instructions of kuberentes
```
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

10) Apply flannel
```
$ sudo kubectl create -f https://raw.githubusercontent.com/coreos/flannel/v0.12.0/Documentation/kube-flannel.yml
```
11) Check kubernetes nodes
```
$ sudo kubectl get nodes
```

![image](https://github.com/vincent51689453/Kubernetes-x86-GPU-Clusters/blob/master/Github_Image/master_setup.png)
