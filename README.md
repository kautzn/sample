# Sample Project Overview

This project demonstrates how to build and deploy a simple Docker image using Nginx within a Kubernetes environment. It includes a comprehensive setup for automating the build and deployment process using GitHub Actions. The workflow handles the following tasks:

- **Docker Image Building**: Creates a Docker image with Nginx, configured to serve static content.
- **Docker Image Pushing**: Pushes the built Docker image to Docker Hub.
- **Kubernetes Deployment**: Deploys the Docker image to a Kubernetes cluster using a deployment.yaml and service.yaml configuration (note that there is currently no cluster defined so the deployment step fails by design).
- **Automated CI/CD Pipeline**: Utilizes GitHub Actions to automate the entire process by leveraging the power of continuous integration and continuous deployment (CI/CD) practices, ensuring that every push to the main branch triggers the build, push, and deployment steps.

At a later point, Terraform for Infrastructure as Code (IaC) to manage AWS resources like EKS, and automated integration and end-to-end (E2E) testing with Robot Framework will be introduced.

## Project Structure

```
sample/
├── .github/
│   └── workflows/
│       └── ci-cd-pipeline.yml
├── src/
│   ├── nginx.conf
│   └── index.html
├── k8s/
│   ├── deployment.yaml
│   ├── service.yaml
├── Dockerfile
├── VERSION
└── README.md
```

## GitHub Actions Workflow

This project includes a GitHub Actions workflow that automatically builds and pushes the Docker image to Docker Hub whenever changes are pushed to the `main` branch, and the Kubernetes configuration files for deploying the image to a Kubernetes cluster will be updated with the new version.

### Workflow Steps

1. **Checkout repository**: The workflow checks out the repository to access the code.
2. **Set up Docker Buildx**: This step sets up Docker Buildx, a tool for building multi-platform Docker images.
3. **Log in to Docker Hub**: The workflow logs in to Docker Hub using the credentials stored in GitHub Secrets.
4. **Extract version from file**: The workflow reads the version number from the `VERSION` file.
5. **Build and push Docker image**: The workflow builds the Docker image and tags it with both the version number and `latest`. It then pushes these tags to Docker Hub.
6. **Deploy to Kubernetes**: The workflow replaces the version placeholder in the Kubernetes deployment file with the actual version and applies the Kubernetes deployment and service files.

### Setting Up GitHub Secrets

To use this workflow, you need to set up the following secrets in your GitHub repository:

- `DOCKER_USERNAME`: Your Docker Hub username.
- `DOCKER_PASSWORD`: Your Docker Hub password.

### Versioning

The Docker image is tagged with the version specified in the `VERSION` file and also with `latest`. This allows you to pull a specific version of the image or always get the latest version.

### Example `VERSION` File

```plaintext
1.0.0
```

## How to Run the Docker Container Locally

1. Build the Docker image:
   ```sh
   docker build -t sample .
   ```

2. Run the Docker container:
   ```sh
   docker run -d -p 8080:80 sample
   ```

3. Open your browser and go to `http://localhost:8080` to see the Nginx welcome page.

## Deploying to Kubernetes

### Kubernetes Deployment

The Kubernetes deployment file (`k8s/deployment.yaml`) uses a placeholder for the image version, which is dynamically replaced during the CI/CD pipeline.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: natekautz/sample:VERSION_PLACEHOLDER
        ports:
        - containerPort: 80
```

### Kubernetes Service

The Kubernetes service file (`k8s/service.yaml`) exposes the Nginx deployment.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

### Applying the Kubernetes Configuration

1. **Ensure you have a Kubernetes cluster running**:
    - You can use Minikube for local development or any cloud provider's Kubernetes service (e.g., GKE, EKS, AKS).

2. **Apply the Deployment and Service files**:
    ```sh
    kubectl apply -f k8s/deployment.yaml
    kubectl apply -f k8s/service.yaml
    ```

3. **Verify the Deployment and Service**:
    ```sh
    kubectl get deployments
    kubectl get services
    ```

4. **Access the Nginx Service**:
    - If you're using a cloud provider, the `LoadBalancer` type Service will provide an external IP address.
    - For Minikube, you can use the following command to get the URL:
      ```sh
      minikube service nginx-service --url
      ```

With this setup, every time you push to the `main` branch, the Docker image will be built, tagged, and pushed to Docker Hub, and the Kubernetes deployment will be updated with the new version.
