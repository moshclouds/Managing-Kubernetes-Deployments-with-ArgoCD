# 🚀 Managing-Kubernetes-Deployments-with-ArgoCD
## 🔥 Introduction

ArgoCD 🚀 is a powerful, declarative GitOps 🔄 continuous delivery tool designed for Kubernetes ☸️, enabling seamless automatic deployment ⚡ and lifecycle management of applications. It ensures that the desired application state, defined in a Git repository 📂, is automatically synchronized with the actual state in a Kubernetes cluster 🏗️. This makes deployments more reliable, automated, and version-controlled ✅. With ArgoCD, teams can achieve real-time monitoring 📊, rollback capabilities 🔄, and multi-cluster management 🌍, enhancing both security 🔐 and efficiency in DevOps workflows.

In this beginner-friendly guide 📖, we will walk through the installation and configuration of ArgoCD on Minikube 💻, a lightweight Kubernetes environment ideal for testing and development. We will then demonstrate how to deploy an application 🚀, monitor its deployment status ✅, and expose it for external access 🌍. By the end of this guide, you will have a fully functional ArgoCD setup 🎯, understand its key features, and be equipped to manage Kubernetes applications declaratively using GitOps principles 🔥.

This guide is **beginner-friendly** and covers essential steps in detail. 🎯

---

## ✅ Prerequisites

Ensure you have the following installed:

- **Minikube** ([Install Guide](https://minikube.sigs.k8s.io/docs/start/))
- **kubectl** ([Install Guide](https://kubernetes.io/docs/tasks/tools/))

To verify Minikube installation:
```sh
minikube version
```

---

## ⚙️ Step 1: Start Minikube

Start your Minikube cluster:
```sh
minikube start
```

Check the status:
```sh
minikube status
```

---

## 📦 Step 2: Create Namespaces

Create a namespace for ArgoCD:
```sh
kubectl create namespace argocd
```

Create a namespace for applications:
```sh
kubectl create namespace argocd-apps
```

---

## 🚀 Step 3: Install ArgoCD

Apply the ArgoCD installation manifest:
```sh
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verify installation:
```sh
kubectl get pods -n argocd -w
```

Check services:
```sh
kubectl get svc -n argocd
```

---

## 🌐 Step 4: Expose ArgoCD Server

By default, the ArgoCD server runs as a ClusterIP service. Change it to **NodePort**:
```sh
kubectl edit svc argocd-server -n argocd
```

Locate `type: ClusterIP` and change it to `type: NodePort`. Save and exit.

Expose the ArgoCD server:
```sh
minikube service argocd-server -n argocd
```

You will receive a URL similar to:
```
http://127.0.0.1:6516
```
Use this URL to access the ArgoCD dashboard. 🎯

---

## 🔑 Step 5: Retrieve ArgoCD Admin Password

Find the secret containing the initial admin password:
```sh
kubectl get secrets -n argocd
```

Edit the secret file:
```sh
kubectl edit secret argocd-initial-admin-secret -n argocd
```

Decode the base64 password using or any other third party services:
```sh
echo <base64-password> | base64 --decode
```

Use `admin` as the username and the decoded password to log in.

---

## 📌 Step 6.1: Deploy Application via ArgoCD(CLI)

1️⃣ **Login to ArgoCD CLI**:
```sh
argocd login <your-minikube-ip>:<argo-port>
```

2️⃣ **Create an Application in ArgoCD**:
```sh
argocd app create "hello-argocd" \
  --repo https://github.com/argoproj/argocd-example-apps.git \
  --path guestbook \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace argocd-apps
```

3️⃣ **Sync the Application**:
```sh
argocd app sync "hello-argocd"
```

4️⃣ **Verify Application Status**:
```sh
kubectl get pods -n argocd-apps
```

---

## 🖥️ Step 6.2: Deploy Application via ArgoCD (GUI)

1️⃣ **Access ArgoCD Web UI**:  
Open the ArgoCD dashboard in your browser using the URL from Step 4:  
```
http://127.0.0.1:6516
```

<img width="1923" alt="Image" src="https://github.com/user-attachments/assets/3c0e45fc-493b-4b38-a649-268da3c14c65" />

<br>

2️⃣ **Login**:  
Use `admin` as the username and the password obtained in Step 5.

3️⃣ **Create an Application**:  
- In the ArgoCD dashboard, click on **+ NEW APP**.
- In the **Application Name** field, enter 

    ```sh
    hello-argocd
    ```
- Set the **Project** to 

    ```sh
    default
    ```
- Under **Repository URL**, enter 

    ```sh
    https://github.com/argoproj/argocd-example-apps.git
    ```
- Set **Path** to 
    ```sh
    guestbook
    ```
- Set **Destination Cluster** to 
    ```sh
    https://kubernetes.default.svc
    ```
- Set **Namespace** to 
    ```sh
    argocd-apps
    ```

Click **Create** to add the application.

<img width="1870" alt="Image" src="https://github.com/user-attachments/assets/d0e35e6c-9b64-4efe-9ffc-4338f02e87e8" />

<img width="1878" alt="Image" src="https://github.com/user-attachments/assets/5cbb9b54-4aee-47ea-a0c1-7093fd67ed19" />

<img width="1857" alt="Image" src="https://github.com/user-attachments/assets/b1cc375d-9a99-4f9d-86fd-c0faf27b107b" />

<br>

4️⃣ **Sync the Application**:  
Once the application is created, click the **Sync** button on the application details page to sync the app.

5️⃣ **Verify Application Status**:  
You will see the status of your application under **Application Details**. Wait until the status shows as **Synced** and **Healthy**.

<img width="1888" alt="Image" src="https://github.com/user-attachments/assets/16778f06-7fa4-44ec-9073-e2ff3af0bab5" />

<img width="1917" alt="Image" src="https://github.com/user-attachments/assets/2067de78-5559-44ef-bcb4-043834a5802e" />

---

## 🌍 Step 7: Expose the Guestbook Application

Find the service:
```sh
kubectl get svc -n argocd-apps
```

Edit the service to get the NodePort:
```sh
kubectl edit svc guestbook-ui -n argocd-apps
```

Expose the application:
```sh
kubectl expose service guestbook-ui --type=NodePort --target-port=80 --name=guestbook-ui-ext -n argocd-apps
```

Access the application:
```sh
minikube service guestbook-ui-ext -n argocd-apps
```

🎉 Your application is now deployed via ArgoCD!

<img width="1910" alt="Image" src="https://github.com/user-attachments/assets/b8685473-30c6-4422-b4a3-f183d9e79053" />

---

## 🎯 Conclusion

Congratulations! You have successfully set up **ArgoCD on Minikube**, deployed an application, and exposed it.

✅ **Key Takeaways:**
- ArgoCD is a **GitOps continuous delivery tool** for Kubernetes.
- Applications are managed declaratively from a **Git repository**.
- Minikube helps simulate a **real Kubernetes cluster** for testing.

🚀 **Next Steps:**
- Explore **Argo Rollouts** for advanced deployment strategies.
- Set up **RBAC and authentication** for ArgoCD security.
- Deploy a **real-world application** using ArgoCD.

---

🔗 Useful Links:
- [ArgoCD Docs](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD GitHub](https://github.com/argoproj/argo-cd)
- [Minikube Docs](https://minikube.sigs.k8s.io/docs/start/)
