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

```
# Assuming you have a virtual environment activated  
pip install -r requirements.txt
```

### **2.2. Building the Docker Image**

Build the container image using the provided Dockerfile. We tag the image as fastapi-app:latest.

```
docker build -t fastapi-app .
```

### **2.3. Local Container Testing**

Run the newly built image locally to ensure it is functioning correctly.

```
docker run -p 8000:8000 fastapi-app
```

The application should now be accessible at http://localhost:8000.

## **3\. Security Scan with Trivy**

Before deploying, it is best practice to scan your image for known vulnerabilities using Trivy. This requires running the Trivy container with access to your host's Docker daemon via the socket mount.

```
docker run \  
  --rm \  
  -v /var/run/docker.sock:/var/run/docker.sock \  
  -v trivy-cache:/root/.cache/ \  
  aquasec/trivy \  
  image \  
  --severity HIGH,CRITICAL \  
  fastapi-app:latest
  ```

Review the output for any HIGH or CRITICAL vulnerabilities.

## **4\. Deployment to Minikube**

To deploy your local image to Minikube, you must first ensure the cluster is running, load the image into its internal registry, and then apply the Kubernetes configuration files (deployment.yaml and service.yaml).

### **4.1. Start Minikube Cluster**

Ensure your local Kubernetes environment is active:

```
minikube start
```

### **4.2. Load Image into Minikube**

This command transfers the image from your local Docker host into the Minikube VM, allowing Kubernetes to access it without pulling from a remote registry.

```
minikube image load fastapi-app:latest
```

### **4.3. Apply Kubernetes Manifests**

Apply the deployment and service configurations in a single command.

```
kubectl apply -f deployment.yaml -f service.yaml
```

### **4.4. Verify Deployment Status**

Check that the application Pod is running successfully before attempting to access the service. Look for the Pod status to be Running.

```
kubectl get pods
```

### **4.5. Access the Application**

Once deployed, use minikube service to get the external URL for your application.

```
minikube service fastapi-service --url
```

The output will be the URL you can use in your browser to access the deployed FastAPI application.

## **5\. Cluster Cleanup**

After testing or development, you can stop the Minikube cluster to free up system resources.

```
minikube stop
```

## **Minikube Configuration Files**

### **deployment.yaml**

See the deployment.yaml file for the full manifest.

### **service.yaml**

See the service.yaml file for the full manifest.