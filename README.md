![Benefits-of-DevOps](https://github.com/user-attachments/assets/4571be51-2359-45d7-bc40-23b578a25103)


# A Complete DevOps Workflow Three-Tier Architecture Deployment

This project implements a robust three-tier application architecture, deployed within a Kubernetes cluster, focusing on modern DevOps practices such as CI/CD pipelines, secure secrets management, and comprehensive monitoring. The goal is to enhance scalability, resilience, and security in the deployment pipeline, leveraging tools like Jenkins, Prometheus, Grafana, Helm, and Sealed Secrets for better control and automation.

## Environment Setup

### Set Up KinD Cluster

1. **Install KinD:** Follow the official KinD installation guide to set up KinD on your system.
2. **Create a KinD Cluster:** Once KinD is installed, create a Kubernetes cluster with the following command:

    ```bash
    kind create cluster --name devops-cluster
    ```

## Docker
The next step involves containerizing the application components. We will build, push, and verify Docker images for the frontend, backend, and database components of the application.

1. **Build Docker Image:** From the project directory, build Docker images for the application components. For example:

    ```bash
    docker build -t <your-dockerhub-username>/3-tier-app:tag .
    ```

2. **Push Docker Image:** Push the images to Docker Hub or your preferred container registry:

    ```bash
    docker push <your-dockerhub-username>/3-tier-app:tag
    ```

3. **Run Docker Container:** Verify the container by running it locally:

    ```bash
    docker run -p <port>:<port> <your-dockerhub-username>/3-tier-app:tag
    ```

4. **Verify Container:** Once the container is running, you can access the application by navigating to the provided URL in your browser.

### Kubernetes Deployment

In Kubernetes, deployments manage the lifecycle of applications, ensuring that the desired number of replicas of a pod are always running.

1. **Deployment YAML:** Define the deployment specifications, including the container image and number of replicas.
2. **Apply the Deployment:** Deploy your application to the KinD cluster:

    ```bash
    kubectl apply -f <deployment_name>.yaml
    ```

3. **Verify Deployment and Pods:** Ensure the deployment is successful and the pods are running:

    ```bash
    kubectl get deployments
    kubectl get pods
    ```

### Ingress

Ingress controllers manage external access to services in Kubernetes, allowing you to route traffic to the correct service.

1. **Install NGINX Ingress Controller:** This step configures ingress rules to expose services externally.

    ```bash
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
    ```

2. **Create and Apply Ingress YAML:** Define the ingress rules for your services and apply them:

    ```bash
    kubectl apply -f <ingress_name>.yaml
    ```

3. **Access Application Externally:** Once the ingress is configured, you can add an entry in your `/etc/hosts` file to map `todos.local` to the KinD node’s IP address. Find the IP address using:

    ```bash
    kubectl get nodes -o wide
    ```

### ConfigMap and Secret

Configuration and secrets management are essential for separating sensitive data from the application code.

1. **ConfigMap YAML:** Define the application configuration data in a `configmap.yaml` file.
2. **Secret YAML:** Define sensitive data such as database credentials in a `secret.yaml` file.
3. **Apply ConfigMap and Secret:** Apply both resources to the cluster:

    ```bash
    kubectl apply -f <configmap_name>.yaml
    kubectl apply -f <secret_name>.yaml
    ```

### PersistentVolume and PersistentVolumeClaim

Persistent volumes (PV) ensure that data is retained even if a pod is deleted or recreated.

1. **PersistentVolume YAML:** Define the storage capacity and access modes for persistent storage in the `pv.yaml` file.
2. **PersistentVolumeClaim YAML:** Create a claim for the required storage in `pvc.yaml`.
3. **Apply PV and PVC:** Apply both resources to the cluster:

    ```bash
    kubectl apply -f pv.yaml
    kubectl apply -f pvc.yaml
    ```

### Securing Secrets with Sealed Secrets

To securely handle sensitive data like API keys and database passwords, Sealed Secrets allow us to manage encrypted secrets that are only decrypted inside the cluster.

1. **Install Sealed Secrets Controller:** Use Helm to install the Sealed Secrets controller.

    ```bash
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm repo update
    helm install sealed-secrets bitnami/sealed-secrets --namespace kube-system
    ```

2. **Seal the Secrets:** Encrypt your secrets with `kubeseal`.

    ```bash
    kubeseal --cert=sealed-secrets-public-key.pem --format yaml < secret.yaml > sealed-secret.yaml
    ```

3. **Apply Sealed Secrets:** Apply the sealed secrets to the cluster.

    ```bash
    kubectl apply -f sealed-secret.yaml
    ```

## Monitoring and Logging

Effective monitoring and logging are essential in a production environment to ensure the health and performance of applications. With tools like Prometheus and Grafana, monitoring becomes an integrated part of the DevOps pipeline, enabling proactive identification of issues and better decision-making.

1. **Install Prometheus and Grafana:** Install both monitoring tools using Helm:

    ```bash
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm install prometheus prometheus-community/prometheus
    helm install grafana grafana/grafana
    ```

2. **Accessing Grafana:** After the installation, access the Grafana dashboard. The default login credentials for Grafana are `admin` for both the username and password. It will prompt you to change the password upon first login.

3. **Configure Prometheus as a Data Source:** In Grafana, configure Prometheus as a data source by selecting Prometheus and providing the appropriate connection details (usually the internal service URL of Prometheus in the cluster).

4. **Create Dashboards:** Grafana allows you to create customized dashboards to visualize the metrics collected by Prometheus. Pre-configured Kubernetes dashboards can be imported to monitor various aspects like CPU and memory usage, pod health, and more.

5. **Set Up Alerts:** Alerts in Grafana can be configured to notify you when certain metrics cross predefined thresholds. This helps you stay informed about critical issues and take immediate actions if necessary.

## Deploying Jenkins using Ansible on a KinD Cluster

Jenkins automates the process of building, testing, and deploying applications, making it an essential tool for DevOps pipelines. Deploying Jenkins in a Kubernetes environment allows for scalable and efficient automation. Using Ansible, the setup process is automated, ensuring consistency and reducing manual errors.

### Jenkins CI/CD Automation

Jenkins helps automate various stages of the software delivery pipeline, from code integration to deployment. By automating the pipeline, teams can release software faster and with fewer errors.

1. **Ansible Setup:** Create an Ansible playbook that defines the Kubernetes resources required for Jenkins, such as the service account, deployment, persistent volume, and necessary permissions.

2. **Playbook Execution:** Run the Ansible playbook to automatically deploy Jenkins to your KinD cluster:

    ```bash
    ansible-playbook playbook.yml
    ```

   The playbook will:
   - Create a Jenkins service account.
   - Set up a Jenkins service and deployment.
   - Ensure persistent storage for Jenkins using PersistentVolume and PersistentVolumeClaim.

3. **Automated Deployment:** Once the playbook runs, Jenkins will be deployed and ready to manage your build and deployment pipelines.

### Integrating Jenkins Pipeline with Git Repository and Setting Up a Webhook

#### Webhook Integration

Jenkins can automatically trigger a build when there is a push to a Git repository. Here's how to integrate the pipeline with GitHub or GitLab:

1. **Set Up Webhooks:** Go to the repository settings and add a webhook pointing to your Jenkins server, configured to listen for push events.
2. **Pipeline Trigger:** In your Jenkins pipeline configuration, set up the necessary steps to pull the code from the Git repository, build it, run tests, and deploy it.

This integration helps in automating the entire software delivery process, ensuring continuous integration and delivery with minimal manual intervention.

