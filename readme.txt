# Start Minikube
minikube start

# Create a namespace for ArgoCD
kubectl create namespace argocd

# Create a namespace for ArgoCD application deployments
kubectl create namespace argocd-apps

# Install ArgoCD using the official manifest
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Verify the installation of ArgoCD
kubectl get pods -n argocd -w

# Check the services
kubectl get svc -n argocd

# Edit the ArgoCD server service to use NodePort
kubectl edit svc argocd-server -n argocd

# Expose the ArgoCD server
minikube service argocd-server -n argocd

# Access the ArgoCD dashboard using the Minikube-provided address
# Example: http://127.0.0.1:6516

# Retrieve secrets to find the ArgoCD admin password
kubectl get secrets -n argocd

# Access the secret file
kubectl edit secret argocd-initial-admin-secret -n argocd

# Decode the base64 password using a third-party service or a command-line tool

# Log in to the ArgoCD dashboard

# Create application configurations in ArgoCD and deploy the app

# Verify the services are up and running
kubectl get svc -n argocd-apps

# Get the port of the service by editing the service configuration
kubectl edit svc guestbook-ui -n argocd-apps

# Expose the application
kubectl expose service guestbook-ui --type=NodePort --target-port=80 --name=guestbook-ui-ext -n argocd-apps

# Run the application using Minikube tunneling
minikube service guestbook-ui-ext -n argocd-apps