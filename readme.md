✅ Step-by-Step Guide for IAC-3
1. Prerequisites
Make sure you have the following installed and set up:

A running Kubernetes cluster (preferably NOT Minikube or Docker Desktop).

kubectl configured to point to your cluster.

helm installed.

Argo CD installed on the cluster.

Your GitHub repo already contains the proxy/, wp/, and db/ folders with correct Kubernetes manifests from IAC-2.

2. Install Argo CD
If Argo CD is not already installed:

bash
Copy
Edit
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
3. Access Argo CD UI
Port forward Argo CD API server to access the UI:

bash
Copy
Edit
kubectl port-forward svc/argocd-server -n argocd 8080:443
Access via https://localhost:8080

Initial username: admin

Get initial password:

bash
Copy
Edit
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
4. Structure Your Git Repository for Argo CD
Keep your current structure like this:

markdown
Copy
Edit
/proxy
  - deployment.yaml
  - service.yaml
  - nginx-config.conf
  - Dockerfile
/wp
  - deployment.yaml
  - service.yaml
/db
  - deployment.yaml
  - service.yaml
Optional: Create a Helm chart if needed. For now, Argo CD can read these raw manifests.

5. Create Argo CD App
You can create an Argo CD app either via CLI or UI.

Option 1: Via CLI
bash
Copy
Edit
argocd app create wordpress-stack \
  --repo https://github.com/<your-github-username>/<your-repo> \
  --path ./ \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default \
  --sync-policy automated \
  --self-heal
Option 2: Via Argo CD UI
Click NEW APP

Name: wordpress-stack

Project: default

Sync Policy: Automated

Repository URL: <your GitHub repo URL>

Revision: main

Path: ./

Cluster: https://kubernetes.default.svc

Namespace: default or custom

6. Verify Argo CD Sync
Once your app is created, it will automatically sync and deploy your manifests to the cluster. Check the Argo CD UI for sync status.

7. Update Manifests to Trigger Deployment
Make a small change to any manifest, such as updating image tags in wp/deployment.yaml:

yaml
Copy
Edit
image: wordpress:6.1
Commit and push this change to your GitHub repo:

bash
Copy
Edit
git add .
git commit -m "Update Wordpress image version"
git push
✅ Argo CD will automatically detect this change and redeploy the application.

8. Database Configuration
Update the environment variables in your db/deployment.yaml as required:

yaml
Copy
Edit
env:
  - name: MYSQL_ROOT_PASSWORD
    value: c0928743
  - name: MYSQL_DATABASE
    value: c0928743
  - name: MYSQL_USER
    value: 3324_user
  - name: MYSQL_PASSWORD
    value: c0928743
9. Clean Labels and Namespace Setup
Double-check that:

Each manifest is using the correct namespace (proxy, wp, db).

Labels are correctly applied:

id: c0928743

app: wp-app (or db, or proxy)

env: dev

Create namespaces if not done already:

bash
Copy
Edit
kubectl create ns proxy
kubectl create ns wp
kubectl create ns db
10. Final Checklist Before Submission
✅ Git repo contains well-structured manifest folders
✅ Correct labels and namespaces in YAML
✅ Argo CD UI shows successful sync
✅ Argo CD redeploys on Git commit
✅ Wordpress is accessible via exposed Nginx proxy

Want me to review or fix anything in your extracted files for this setup?








