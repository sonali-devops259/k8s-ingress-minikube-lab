# Kubernetes Ingress Hands-on (Minikube) — Services → Ingress → Path Rewrite

This lab demonstrates:
- ClusterIP services discovering pods via EndpointSlice
- NodePort exposure (Minikube)
- Ingress Controller (NGINX) routing by Host + Path
- Fixing common 404 issue using path rewrite

## Prerequisites
- Docker Desktop running
- Minikube + kubectl installed

## 1) Start Minikube

minikube start --driver=docker
2) Create two apps (as sample microservices)
kubectl create deployment payments --image=nginx
kubectl create deployment orders --image=nginx

kubectl scale deployment payments --replicas=2
kubectl scale deployment orders --replicas=2

kubectl expose deployment payments --port=80 --target-port=80
kubectl expose deployment orders --port=80 --target-port=80
3) Enable NGINX Ingress Controller
minikube addons enable ingress
kubectl get pods -n ingress-nginx
4) Apply Ingress (Host + Path routing + rewrite)
Apply:

kubectl apply -f ingress.yaml
kubectl get ingress
kubectl describe ingress app-ingress

5) Access Ingress on Windows (Docker driver limitation workaround)

On Windows + Minikube Docker driver, NodePort/Minikube IP may not be reachable directly.
Use port-forward:

kubectl -n ingress-nginx port-forward svc/ingress-nginx-controller 8080:80


Then test:

curl.exe -H "Host: app.local" http://127.0.0.1:8080/payments
curl.exe -H "Host: app.local" http://127.0.0.1:8080/orders


Optional hosts entry (for browser):

Add to C:\Windows\System32\drivers\etc\hosts:

127.0.0.1 app.local


Browser:

http://app.local:8080/payments

http://app.local:8080/orders

4) Git commands (push to GitHub)

From inside the repo folder:

git init
git add .
git commit -m "K8s Ingress on Minikube: host/path routing + rewrite"
git branch -M main
git remote add origin https://github.com/<your-username>/k8s-ingress-minikube-lab.git
git push -u origin main

Cleanup
kubectl delete ingress app-ingress
kubectl delete svc payments orders
kubectl delete deployment payments orders
