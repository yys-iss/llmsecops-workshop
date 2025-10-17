# **FastAPI Application Deployment Guide**

This guide details the prerequisites, installation, local testing, security scanning, and deployment of the fastapi-app container onto a local Minikube Kubernetes cluster.

## **1\. Prerequisites**

Ensure you have the following software installed on your system:

* **Python 3.13** (or compatible version)  
* **Docker Desktop** (or equivalent Docker Engine)  
* **Minikube** (and kubectl for interacting with the Kubernetes cluster)

## **2\. Local Installation and Setup**

### **2.1. Python Environment Setup**

If you need to work with the Python source code directly, install dependencies using pip:

\# Assuming you have a virtual environment activated  
pip install \-r requirements.txt

### **2.2. Building the Docker Image**

Build the container image using the provided Dockerfile. We tag the image as fastapi-app:latest.

docker build \-t fastapi-app .

### **2.3. Local Container Testing**

Run the newly built image locally to ensure it is functioning correctly.

docker run \-p 8000:8000 fastapi-app

The application should now be accessible at http://localhost:8000.

## **3\. Security Scan with Trivy**

Before deploying, it is best practice to scan your image for known vulnerabilities using Trivy. This requires running the Trivy container with access to your host's Docker daemon via the socket mount.

docker run \\  
  \--rm \\  
  \-v /var/run/docker.sock:/var/run/docker.sock \\  
  \-v trivy-cache:/root/.cache/ \\  
  aquasec/trivy \\  
  image \\  
  \--severity HIGH,CRITICAL \\  
  fastapi-app:latest

Review the output for any HIGH or CRITICAL vulnerabilities.