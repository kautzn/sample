# This simple project

This is a simple project to run a basic Docker image using Nginx.

In the future, the goal is to add testing, Kubernetes, and terraform

## Project Structure

sample/ ├── .github/ │ └── workflows/ │ └── docker-build.yml ├── src/ │ ├── nginx.conf │ ├── index.html ├── Dockerfile ├── VERSION └── README.md

## How to Run Locally

1. Build the Docker image:
   ```sh
   docker build -t sample .
   ```

2. Run the Docker container:
   ```sh
   docker run -d -p 8080:80 sample
   ```

3. Open your browser and go to `http://localhost:8080` to see the Nginx welcome page.

## GitHub Actions Workflow

This project includes a GitHub Actions workflow that automatically builds and pushes the Docker image to Docker Hub whenever changes are pushed to the `main` branch.

### Workflow Steps

1. **Checkout repository**: The workflow checks out the repository to access the code.
2. **Set up Docker Buildx**: This step sets up Docker Buildx, a tool for building multi-platform Docker images.
3. **Log in to Docker Hub**: The workflow logs in to Docker Hub using the credentials stored in GitHub Secrets.
4. **Extract version from file**: The workflow reads the version number from the `VERSION` file.
5. **Build and push Docker image**: The workflow builds the Docker image and tags it with both the version number and `latest`. It then pushes these tags to Docker Hub.

### Setting Up GitHub Secrets

To use this workflow, you need to set up the following secrets in your GitHub repository:

- `DOCKER_USERNAME`: Your Docker Hub username.
- `DOCKER_PASSWORD`: Your Docker Hub password.

### Versioning

The Docker image is tagged with the version specified in the `VERSION` file and also with `latest`. This allows you to pull a specific version of the image or always get the latest version.

### Example `VERSION` File

1.0.0
