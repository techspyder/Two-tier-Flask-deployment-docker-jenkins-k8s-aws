# Two-tier-Flask-deployment-docker-jenkins-k8s-aws
This is a repo for two-tier flask app deployment on k8s using docker and Jenkins.

# Setting up Amazon EKS Cluster with Kubectl and Eksctl

In this guide, we'll walk through the process of creating an Amazon EKS cluster and configuring `kubectl` and `eksctl` on an Amazon EC2 instance. Amazon EKS simplifies Kubernetes management by handling the Kubernetes control plane, ensuring high availability and scalability.

## Prerequisites

Before getting started, make sure you have the following prerequisites in place:

1. An AWS account with administrative access.

2. An IAM user with programmatic access. Note the access key and secret access key for this user.

3. An Amazon EC2 instance with the AWS CLI installed and configured with the IAM user's credentials.

## Installation and Configuration

### Install Kubectl

On your EC2 instance, install `kubectl` using the following commands:

```bash
# Update the package list and install kubectl
$ sudo apt-get update && sudo apt-get install -y kubectl

# Download and install eksctl
$ sudo wget https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_Linux_amd64 -O /usr/local/bin/eksctl
$ sudo chmod +x /usr/local/bin/eksctl

##Configure AWS CLI
$ aws configure
AWS Access Key ID [None]: YourAccessKey  ; You got it after creating an IAM user.
AWS Secret Access Key [None]: YourSecretKey
Default region name [None]: us-west-2
Default output format [None]: json

#Creating an Amazon EKS Cluster
# Create an EKS cluster
$ eksctl create cluster --name my-cluster --region us-west-2 --node-type t2.micro --nodes-min 2 --nodes-max 2
# Update kubeconfig
$ aws eks update-kubeconfig --region us-west-2 --name my-cluster
# Verify cluster nodes
$ kubectl get nodes

# Two-Tier Application Deployment on AWS EKS with Kubernetes

This guide explains the deployment of a two-tier application on Amazon EKS (Elastic Kubernetes Service) using Kubernetes. The two tiers consist of a Flask-based web application and a MySQL database. The deployment is divided into multiple Kubernetes resource files.

## Kubernetes Resource Files

1. **two-tier-app-svc.yml**: Defines a Kubernetes Service named `two-tier-app-service`. It exposes the web application to external traffic using a LoadBalancer service on port 80, forwarding requests to pods labeled with `app: two-tier-app`.

2. **two-tier-app-deployment.yml**: Specifies a Kubernetes Deployment named `two-tier-app`. It deploys the Flask-based web application with two replicas. The application connects to a MySQL database using environment variables for configuration.

3. **mysql-deployment.yml**: Defines a Kubernetes Deployment named `mysql`. It deploys a MySQL database with a single replica, based on the official `mysql:latest` image. The MySQL instance is configured with the root password obtained from the `mysql-secret` Secret and mounts a volume for executing initialization scripts.

4. **mysql-svc.yml**: Creates a Kubernetes Service named `mysql` that exposes the MySQL database within the cluster. It selects pods labeled with `app: mysql` and exposes port 3306.

5. **mysql-configmap.yml**: Stores an initialization SQL script (`init.sql`) in a ConfigMap. The SQL script creates a database named `mydb` and a table named `messages`. This ConfigMap is referenced in the MySQL deployment to execute these SQL commands during container initialization.

6. **mysql-secrets.yml**: Defines a Secret named `mysql-secret` containing sensitive data, particularly the MySQL root password (`MYSQL_ROOT_PASSWORD`) encoded in base64. The MySQL deployment references this Secret to securely obtain the password.

## Instructions

To deploy this two-tier application on Amazon EKS, apply these Kubernetes resource files in the following order:

1. Apply the MySQL related resources (Secret, ConfigMap, Deployment, and Service) to set up the MySQL database.

2. Apply the web application related resources (Deployment, Service) to set up the Flask-based web application.

Ensure you have configured `kubectl` and `eksctl` as per your AWS environment and have the necessary AWS CLI configuration set up for EKS.

After deploying the resources, you will have a functioning two-tier application on Amazon EKS.

For more advanced configurations and considerations, refer to the Amazon EKS documentation and Kubernetes documentation.
