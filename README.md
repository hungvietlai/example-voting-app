# Example Voting App with Kubernetes in AWS

This repository contains the Kubernetes configurations needed to deploy the Example Voting App in AWS, a simple distributed application that allows users to vote between two choices: cats 🐱 and dogs 🐶.

---

## Table of Contents

- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Deployment](#deployment)
- [Cleanup](#cleanup)
- [Credit](#credit)
- [License](#license)

---

## Architecture

- **Voting App**: A front-end web app through which users can cast their votes.
- **Result App**: A front-end web app displaying the results.
- **Worker**: A Java worker which consumes votes and stores them in a Postgres database
- **Redis**: A Redis queue which collects new votes
- **Postgres**: A Postgres database backed by a Docker volume

![Architecture Diagram](https://github.com/hungvietlai/example-voting-app/blob/main/images/voting-app-architecture.png)

---

## Prerequisites

- Ensure you have the following tools installed:

- AWS CLI
- eksctl
- kubectl
- See the link below for guide on "Getting started with Amazon EKS" 

(https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html)

- Once installed, verify your installations with:

```bash
aws --version
eksctl version
kubectl version
```

- You also need to configure 'aws' with your credentials:

```bash
aws configure
```
- This will prompt you to enter your AWS access key, secret key, region, and output format. You can find the relevant informations from your IAM service.

---

## Deployment

### 1. Create an EKS Cluster:

```bash
 eksctl create cluster --name my-voting-app-cluster --version 1.28 --region us-east-1 --nodegroup-name standard-workers --node-type t2.micro --nodes 3 --zones us-east-1a,us-east-1b --managed
 ```
 - This command will create a managed EKS cluster with a managed node group. The node group will have 3 **t2.micro** nodes in the region of **us-east-1**.  Adjust the region as needed.

#### 1.1 Verify Cluster Creation:

 ```bash
 eksctl get clusters
 ```
### 2. Set your **kubectl** contect:

- This will update your kubectl so it will know which cluster to manage.

```bash
aws eks --region us-east-1 update-kubeconfig --name my-voting-app-cluster
```

#### 2.1 Verify your nodes:

```bash
kubectl get nodes
```
- There should be 3 nodes.

### 3.Deploy the Voting App:

```bash
git clone https://github.com/hungvietlai/example-voting-app.git
cd cd k8s-yaml-files/
for file in *.yaml; do kubectl create -f $file; done
```
#### 3.1 Check deployments, services:

```bash
kubectl get deploy,svc
```
- You should observe 5 deployments and 4 services.

### 4. Access the Voting App:

- Access the voting and results apps via any node's public IP on ports 30001 and 30002, respectively.

- **Note**: If you can't access the app, review the security settings of the node in the EC2 dashboard. Ensure inbound rules allow traffic on ports 30001 and 30002.

<p float="left">
  <img src="https://github.com/hungvietlai/example-voting-app/blob/main/images/voting-image.png" width="400" />
  <img src="https://github.com/hungvietlai/example-voting-app/blob/main/images/result-image.png" width="400" /> 
</p>

## Clean Up:

- When done testing, to avoid unnecessary costs, delete the cluster:

```bash
eksctl delete cluster --name my-voting-app-cluster
```
- **Note**: This includes charges for Amazon EKS, which is $0.10/hour, and for the Amazon Elastic Compute Cloud NatGateway, which costs $0.045/hour, plus VAT.

## Credit

- This example is adapted from the official Kubernetes documentation. Learn more [here](https://kubernetes-bootcamp.wikitops.io/exercise-app/voting-app)

## Licensing 

This project is licensed under the MIT License. For the full text of the license, see the LICENSE file.