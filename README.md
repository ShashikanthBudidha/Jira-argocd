Note: This repository is designed for an on-premises Kubernetes cluster. You may need to adjust the configurations to match your own Kubernetes environment.
Since this setup does not rely on a cloud storage provider, the required volumes (e.g., shared home and local home) have been manually created. If you are deploying on a cloud-based cluster or a different storage backend, update the volume definitions accordingly.

Prerequisites

Before deploying Jira on Kubernetes using Argo CD, make sure you have the following:

📘 Knowledge Requirements

Basic understanding of Kubernetes concepts

Familiarity with the Helm package manager

🛠️ Tools & Versions

kubectl v1.21 or later (must be compatible with your Kubernetes cluster).

helm v3.3 or later

🏗️ Infrastructure Requirements

Storage: Configured shared home and local home volumes for Jira (make use of the files available in storage folder for creating PVC for shared and local home).

Database: A compatible database version supported by the Jira release you are deploying (refer to Atlassian Supported Platforms). reference for compatibility: https://confluence.atlassian.com/adminjiraserver/supported-platforms-938846830.html


step-1:
Installing Argo CD on Kubernetes

Create a Namespace for Argo CD 
kubectl create namespace argocd

Install Argo CD Components

Apply the official Argo CD manifests:

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

This installs all the Argo CD components (API server, controller, repo server, UI, etc.).

Verify Installation
Check that pods are running:
kubectl get pods -n argocd

Configure an Ingress resource for Argo CD to enable browser access via your chosen domain (use the file given ingress folder)
kubectl apply <ingress file yaml> -n argocd

Step 2
Create Argo CD Application for deploying Jira using Helm
Purpose: Create an Argo CD Application that instructs Argo CD to deploy the Jira Helm chart into your target Kubernetes namespace/cluster, and to manage future updates automatically if desired.

Add the Helm chart repository
helm repo add atlassian-data-center \
 https://atlassian.github.io/data-center-helm-charts

Add git repository to Argo CD: 
Make sure that you have installed public key in your GitHub account
argocd repo add git@github.com:<your-org>/<your repo>.git --ssh-private-key-path ~/.ssh/private_key

Create the Application( use the file available in argocd folder) 
Apply using kubectl: 
kubectl apply -f argoapp-jira.yaml

