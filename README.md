 
# Flask App with MySQL Docker Setup

This is a simple Flask app that interacts with a MySQL database. The app allows users to submit messages, which are then stored in the database and displayed on the frontend.

- Frontend/Logic Tier: Flask web application (Dockerized).
- Backend Tier: MySQL database running in Kubernetes.
- Orchestration: AWS EKS with eksctl.
- Exposure: Application is exposed externally using a Kubernetes LoadBalancer Service.

## Prerequisites

Before you begin, make sure you have the following installed:

- Docker
- Git (optional, for cloning the repository)
- eksctl
- kubectl
- AWS CLI

## Steps to Deploy

1. Clone this repository (if you haven't already):

   ```bash
   git clone https://github.com/Yunus705/two-tier-flask-app.git
   ```

2. Navigate to the project directory:

   ```bash
   cd two-tier-flask-app
   ```

3. Build and Push Flask App Image:

   ```bash
   docker build -t <dockerhub-username>/flaskapp:latest .
   docker push <dockerhub-username>/flaskapp:latest
   ```

4. Create EKS Cluster:

   ```bash
   eksctl create cluster --name three-tier-cluster --region us-west-2 --node-type t2.medium --nodes-min 2 --nodes-max 2
   aws eks update-kubeconfig --region us-west-2 --name three-tier-cluster
   kubectl get nodes
   ```

5. Apply Kubernetes Manifests:

   ```bash
   kubectl apply -f mysql-secrets.yml -f mysql-configmap.yml -f mysql-deployment.yml -f mysql-service.yml
   kubectl apply -f two-tier-app-deployment.yml -f two-tier-app-service.yml
   ```

6. Verify Resources:

   ```bash
   kubectl get pods
   kubectl get svc
   ```

7. Access Application:
   Copy the EXTERNAL-IP of two-tier-app-service (LoadBalancer).
   Open in browser:
   ```
   http://<EXTERNAL-IP>/
   ```

## 🛠 Application Workflow

1. User sends request to Flask app via LoadBalancer.
2. Flask App processes request and connects to MySQL Service.
3. MySQL Pod stores and retrieves messages.