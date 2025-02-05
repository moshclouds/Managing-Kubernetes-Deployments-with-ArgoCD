//start the minikube
minikube start

// create name space for argocd
kubectl create namespace argocd

//install via plain manifest
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

//verify installation of argocd
kubectl get pods -n argocd -w

//checks the services
kubectl get svc -n argocd

//edit the argocd-server to nodeport
kubectl edit svc argocd-server -n argocd

//expose the argocd server 
minikube service argocd-server -n argocd

//access via the mnikube given address
example:-  http://127.0.0.1:6516

//retrive secrets to find out argocd password
kubectl get secrets -n argocd

//acess secret file
kubectl edit secret argocd-initial-admin-secret -n argocd

//decode the base64 password using 3rd partry serrvice or an ohter

//