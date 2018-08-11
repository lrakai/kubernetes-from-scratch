# kubernetes-from-scratch

Demonstrate creating a Kubernetes cluster from scratch on AWS EC2 instances.

![Final Environment](https://user-images.githubusercontent.com/3911650/43988366-7cb33eb4-9cf1-11e8-8969-f8063b626bfc.png)

## Getting Started

Deploy the CloudFormation `infrastructure/cloudformation.json` template. The template creates a user with the following credentials and minimal required permisisons to complete the Lab:

- Username: _student_
- Password: _password_

## Instructions

1. Connect to __instance-a__ using SSH

1. Enter the following commands to install `kubeadm`:

    ```sh
    sudo apt-get update
    sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository \
      "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) \
      stable"
    sudo apt-get update
    sudo apt-get install -y docker-ce=17.03.2~ce-0~ubuntu-xenial
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
    sudo add-apt-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
    sudo apt-get update
    sudo apt-get install -y kubelet=1.10.6-00 kubeadm=1.10.6-00 kubectl=1.10.6-00
    sudo apt-mark hold kubelet kubeadm kubectl
    ```

1. Initialize the master node and install the Calico CNI:

    ```sh
    sudo kubeadm init --pod-network-cidr=192.168.0.0/16
    ```

    Take note of the `join` command in the output.

    ```sh
    kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml
    kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml
    ```

1. Join the worker nodes by connecting to them via SSH and entering the join command in the output of the `init` command but with `sudo` at the beginnging and `--ignore-preflight-errors=cri` at the end to avoid a circular dependency bug.

## Cleaning Up

Delete the CloudFormation stack to remove all the resources used in the Lab.
