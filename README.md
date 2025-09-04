Microservices CI/CD Pipeline (Jenkins + Docker + Kubernetes)
Project Structure
microservices/
├── services/
│   ├── auth/
│   │   ├── Dockerfile
│   │   └── app.py
│   ├── user/
│       ├── Dockerfile
│       └── app.py
├── k8s-manifests/
│   ├── auth-deployment.yaml
│   ├── user-deployment.yaml
│
└── Jenkinsfile

Each service has its own Dockerfile and Kubernetes deployment YAML.

Prerequisites
Jenkins with required plugins:
Pipeline
Docker Pipeline
Credentials Binding
Docker installed on Jenkins node
kubectl installed on Jenkins node
Access to a Kubernetes cluster (EKS, Minikube, etc.)
DockerHub account
Jenkins Credentials Setup
Inside Jenkins → Manage Jenkins → Credentials:

GitHub Credentials

ID: Gitcreds
Type: Username/Password or PAT
DockerHub Credentials

ID: Dockercreds
Type: Username/Password
Kubeconfig

ID: kubeconfig
Type: Secret File
Upload your Kubernetes kubeconfig file
Pipeline Flow
Checkout Code → Fetches repo from GitHub
Build & Push Docker Images → Builds images per service, pushes to DockerHub with BUILD_NUMBER tag
Deploy to Kubernetes → Updates manifests, applies to cluster, verifies rollout
✅ Verification
After a successful run:

kubectl get pods
kubectl get deployments
kubectl get svc
Check rollout status:

kubectl rollout status deployment/auth-deployment
Notes
kubeconfig must be stored as Secret File in Jenkins.

If using EKS, generate kubeconfig with:

aws eks update-kubeconfig --region <region> --name <cluster_name>
Works with both Minikube (local) and EKS (cloud).

