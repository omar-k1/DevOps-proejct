DevOps Best Practices for Business Strategy Success: Final Project

This project implements a robust three-tier application architecture, deployed within a Kubernetes cluster, focusing on modern DevOps practices such as CI/CD pipelines, secure secrets management, and comprehensive monitoring. The goal is to enhance scalability, resilience, and security in the deployment pipeline, leveraging tools like Jenkins, Prometheus, Grafana, Helm, and Sealed Secrets for better control and automation.
Environment Setup
Set Up KinD Cluster

First, we need to establish a local Kubernetes environment using KinD (Kubernetes in Docker), which is perfect for local development and testing.

    Install KinD: Follow the official KinD installation guide to set up KinD on your system.
    Create a KinD Cluster: Once KinD is installed, create a Kubernetes cluster with the following command:

    kind create cluster --name devops-cluster

Docker

The application components need to be containerized. In this section, we build, push, and verify Docker images for the frontend, backend, and database components.

    Build Docker Image: From the project directory, build Docker images for the application components. For example:

docker build -t <your-dockerhub-username>/3-tier-app:tag .

Push Docker Image: Push the images to Docker Hub or your preferred registry:

docker push <your-dockerhub-username>/3-tier-app:tag

Run Docker Container: Verify the container by running it locally:

    docker run -p <port>:<port> <your-dockerhub-username>/3-tier-app:tag

    Verify Container: Ensure the application is accessible by visiting http://localhost:<port> in your browser.

Kubernetes Deployment Summary
Deployments

In Kubernetes, deployments manage the lifecycle of applications, ensuring that the desired number of replicas of a pod are always running.

    Deployment YAML: Define the deployment specifications, including the container image and number of replicas.
    Apply the Deployment: Deploy your application to the KinD cluster:

kubectl apply -f <deployment_name>.yaml

Verify Deployment and Pods: Ensure the deployment is successful and the pods are running:

    kubectl get deployments
    kubectl get pods

Ingress

Ingress controllers manage external access to services in Kubernetes, allowing you to route traffic to the correct service.

    Install NGINX Ingress Controller: This step configures ingress rules to expose services externally.

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

Create and Apply Ingress YAML: Define the ingress rules for your services and apply them:

kubectl apply -f <ingress_name>.yaml

Access Application Externally: Once the ingress is configured, you can add an entry in your /etc/hosts file to map todos.local to the KinD node’s IP address. Find the IP address using:

    kubectl get nodes -o wide

ConfigMap and Secret

Configuration and secrets management are essential for separating sensitive data from the application code.

    ConfigMap YAML: Define the application configuration data in a configmap.yaml file.
    Secret YAML: Define sensitive data such as database credentials in a secret.yaml file.
    Apply ConfigMap and Secret: Apply both resources to the cluster:

    kubectl apply -f <configmap_name>.yaml
    kubectl apply -f <secret_name>.yaml

PersistentVolume and PersistentVolumeClaim

Persistent volumes (PV) ensure that data is retained even if a pod is deleted or recreated.

    PersistentVolume YAML: Define the storage capacity and access modes for persistent storage in the pv.yaml file.
    PersistentVolumeClaim YAML: Create a claim for the required storage in pvc.yaml.
    Apply PV and PVC: Apply both resources to the cluster:

    kubectl apply -f pv.yaml
    kubectl apply -f pvc.yaml

Securing Secrets with Sealed Secrets

To securely handle sensitive data like API keys and database passwords, Sealed Secrets allow us to manage encrypted secrets that are only decrypted inside the cluster.

    Install Sealed Secrets Controller: Use Helm to install the Sealed Secrets controller.

helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install sealed-secrets bitnami/sealed-secrets --namespace kube-system

Seal the Secrets: Encrypt your secrets with kubeseal:

kubeseal --cert=sealed-secrets-public-key.pem --format yaml < secret.yaml > sealed-secret.yaml

Apply Sealed Secrets: Apply the sealed secrets to the cluster:

    kubectl apply -f sealed-secret.yaml

Monitoring and Logging

To effectively monitor the performance and health of your Kubernetes deployment, tools like Prometheus and Grafana can be used to collect metrics and visualize data.

    Install Prometheus and Grafana: Install both monitoring tools using Helm:

    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm install prometheus prometheus-community/prometheus
    helm install grafana grafana/grafana

    Access Grafana: Open Grafana in your browser, and configure Prometheus as the data source.
    Create Dashboards: Build custom dashboards to monitor key metrics such as CPU, memory usage, and application health.
    Set Up Alerts: Configure alerts to notify you when thresholds are crossed, helping you proactively manage issues.

Deploying Jenkins using Ansible on a KinD Cluster
Jenkins CI/CD Automation

Jenkins is a powerful automation server that helps in building, testing, and deploying applications. Automating Jenkins deployment using Ansible streamlines the process and ensures consistency.

    Ansible Setup: Create an Ansible playbook that defines the Kubernetes resources required for Jenkins, such as the service account, deployment, persistent volume, and necessary permissions.

    Playbook Execution: Run the Ansible playbook to automatically deploy Jenkins to your KinD cluster:

    ansible-playbook playbook.yml

    The playbook will:
        Create a Jenkins service account.
        Set up a Jenkins service and deployment.
        Ensure persistent storage for Jenkins using PersistentVolume and PersistentVolumeClaim.

    Automated Deployment: Once the playbook runs, Jenkins will be deployed and ready to manage your build and deployment pipelines. You can access Jenkins through the Ingress or port-forwarding.

Integrating Jenkins Pipeline with Git Repository and Setting Up a Webhook
Webhook Integration

Jenkins can automatically trigger a build when there is a push to a Git repository. Here's how to integrate the pipeline with GitHub or GitLab:

    Set Up Webhooks: Go to the repository settings and add a webhook pointing to your Jenkins server, configured to listen for push events.
    Pipeline Trigger: In your Jenkins pipeline configuration, set up the necessary steps to pull the code from the Git repository, build it, run tests, and deploy it.

This integration helps in automating the entire software delivery process, ensuring continuous integration and delivery with minimal manual intervention.

Let me know if you'd like any further modifications!
