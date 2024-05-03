WebApp for devops automation

Setting Up GitOps Pipeline with Argo CD and Argo Rollouts
This document provides a step-by-step guide to setting up a GitOps pipeline using Argo CD and Argo Rollouts to deploy a web application on a Kubernetes cluster with a canary release strategy.

Step 1: Install Minikube and Start Kubernetes Cluster
Install Minikube:
bash
Copy code
# Install Minikube using a package manager or binary
Start Minikube:
bash
Copy code
minikube start
Step 2: Install Argo CD
Download the Argo CD manifests:
bash
Copy code
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
Expose Argo CD UI:
bash
Copy code
kubectl port-forward svc/argocd-server -n argocd 8080:443
Access Argo CD UI:
arduino
Copy code
http://localhost:8080
Step 3: Install Argo Rollouts
Download the Argo Rollouts manifests:
bash
Copy code
kubectl apply -n argo-rollouts -f https://raw.githubusercontent.com/argoproj/argo-rollouts/stable/manifests/install.yaml
Step 4: Dockerize the Application
Create a Dockerfile for the web application:
Dockerfile
Copy code
FROM nginx:alpine
COPY index.html /usr/share/nginx/html
EXPOSE 80
Build and tag the Docker image:
bash
Copy code
docker build -t yourusername/webapp:latest .
Push the Docker image to Docker Hub:
bash
Copy code
docker push yourusername/webapp:latest
Step 5: Deploy the Application Using Argo CD
Update Kubernetes manifests to use the Docker image:
Modify deployment.yaml with the Docker image.
Create service.yaml for the web application service.
Apply the Kubernetes manifests:
bash
Copy code
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
Create Argo CD application:
yaml
Copy code
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: webapp
  namespace: argocd
spec:
  destination:
    namespace: default
    server: 'https://kubernetes.default.svc'
  project: default
  source:
    repoURL: https://github.com/yourusername/your-repository
    path: webApp
    targetRevision: HEAD
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
Step 6: Implement Canary Release with Argo Rollouts
Define a rollout strategy in deployment.yaml using Argo Rollouts.
Trigger a rollout by updating the Docker image and rollout definition.
Monitor the rollout using Argo Rollouts to ensure the canary release completes successfully.
Step 7: Documentation and Cleanup
Document the process, challenges, and solutions in the README.md file.
Clean up resources:
bash
Copy code
kubectl delete -f argocd-application.yaml
kubectl delete -f https://raw.githubusercontent.com/argoproj/argo-rollouts/stable/manifests/install.yaml
Conclusion
By following these steps, you have successfully set up a GitOps pipeline using Argo CD and Argo Rollouts to deploy and manage your web application on Kubernetes with canary release capabilities.
