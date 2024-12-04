![Benefits-of-DevOps](https://github.com/user-attachments/assets/4571be51-2359-45d7-bc40-23b578a25103)


# A Complete DevOps Workflow Three-Tier Architecture Deployment

This project implements a robust three-tier application architecture, deployed within a Kubernetes cluster, focusing on modern DevOps practices such as CI/CD pipelines, secure secrets management, and comprehensive monitoring. The goal is to enhance scalability, resilience, and security in the deployment pipeline, leveraging tools like **Jenkins**, **Prometheus**, **Grafana**, **Helm**, and **Sealed Secrets** for better control and automation.

## Environment Setup

### Set Up KinD Cluster

- **Install KinD**: Follow the official KinD installation guide to set up KinD on your system.
- **Create a KinD Cluster**: Once KinD is installed, create a Kubernetes cluster with the following comman
      ```bash
    kind create cluster --name devops-cluster
    ```
