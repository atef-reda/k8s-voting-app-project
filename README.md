Kubernetes Microservices Voting Application

This project demonstrates the deployment of a classic multi-container, microservices-based voting application onto a Kubernetes cluster. The architecture includes five distinct services, persistent data, and network routing managed by an NGINX Ingress controller.
🏛️ Architecture

The application is composed of five main microservices that work together:

    Vote App: A Python web frontend for users to cast votes.

    Redis: An in-memory key-value store used as a queue for incoming votes.

    Worker: A .NET background process that consumes votes from Redis and persists them in the database.

    Database: A PostgreSQL database for storing the final vote counts.

    Result App: A Node.js web frontend to display the aggregated voting results.

✅ Prerequisites

Before you begin, ensure you have the following tools installed and configured:

    A running Kubernetes cluster (e.g., Minikube, Kind, or a cloud-based cluster).

    kubectl configured to communicate with your cluster.

    helm (the package manager for Kubernetes).

🚀 Deployment Steps

All required Kubernetes manifests (Deployments, Services, and Ingress) should be located in a manifests/ directory.
1. Deploy the Application Components

First, deploy all the core services and deployments for the voting application.

# Apply all manifests to create the deployments and services
kubectl apply -f manifests/

2. Install an NGINX Ingress Controller

To route external traffic to your application, you need an Ingress controller.

Option A: Minikube Addon (Easiest for Minikube)

minikube addons enable ingress

Option B: Install with Helm (Recommended for other clusters)

# Add the official ingress-nginx repository
helm repo add ingress-nginx [https://kubernetes.github.io/ingress-nginx](https://kubernetes.github.io/ingress-nginx)
helm repo update

# Install the controller in its own namespace
helm install nginx-ingress ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace

3. Apply the Ingress Resource

Once the Ingress controller is running, apply the ingress.yaml manifest. This contains the routing rules for the vote and result applications.

kubectl apply -f manifests/ingress.yaml

🌐 Accessing the Application

There are two primary ways to access the deployed application.
Method 1: Using Ingress (Recommended)

This is the intended way to access the application, using host-based routing.

    Find Your Ingress IP Address:

        If using the Minikube addon, get the cluster's IP:

        minikube ip

        If you installed with Helm, get the external IP of the LoadBalancer service (you may need to run minikube tunnel in a separate terminal for the IP to appear):

        kubectl get service -n ingress-nginx

    Edit Your Local hosts File:
    You need administrator/sudo privileges to edit this file. Add the following lines, replacing <YOUR_INGRESS_IP> with the IP from the previous step.

    <YOUR_INGRESS_IP> vote.example.com
    <YOUR_INGRESS_IP> result.example.com

    Access in Your Browser:

        To vote: http://vote.example.com

        To see results: http://result.example.com

Method 2: Using NodePort (For Direct Access/Debugging)

You can bypass the Ingress and connect directly to the services.

    Get a Node IP Address:

    kubectl get nodes -o wide

    Get the Service NodePorts:

    kubectl get service

    Look for the high-numbered ports for vote-svc and result-svc.

    Access in Your Browser:

        To vote: http://<NODE_IP>:<VOTE_NODE_PORT>

        To see results: http://<NODE_IP>:<RESULT_NODE_PORT>

🧹 Cleanup

To remove all the resources created by this project, run the following commands:

# Delete all application resources from the manifests folder
kubectl delete -f manifests/

# Uninstall the Helm release (if you used Helm)
helm uninstall nginx-ingress -n ingress-nginx

