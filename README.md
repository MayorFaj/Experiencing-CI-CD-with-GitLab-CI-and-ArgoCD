# Experiencing CI/CD with GitLab-CI and ArgoCD

This is a simple Python Flask web application. The app provides system information and a realtime monitoring screen with dials showing CPU, memory, IO and process information.

## Steps

- Build  a simple CI/CD pipeline for the Python Application.

![Image-6d](./Images/Image-6d.png)

![Image-6e](./Images/Image-6e.png)

- Configure GitLab CI for contionous Integration, so the Argo CD automatically picks up the built docker image and run in your cluster.

![Image6a](./Images/Image-6a.png)

![Image-6c](./Images/Image-6c.png)

![Image-6b](./Images/Image-6b.png)

- Configure ArgoCD to connect to the Gitlab repository where the configuration files are hosted.

Let's begin

1. Build a Kubernetes cluster.

2. Deploy ArgoCD in the K8s cluster. (For this project, i am using minikube cluster)

```
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

![Image-1](./Images/Image-1a.png)

Access the argocd-server through the browser

```
kubectl edit service argocd-server -n argocd
```

```
minikube service argocd-server -n argocd 
```

![Imaage-1b](./Images/Image-1b.png)

![Image-1c](./Images/Image-2c.png)

- Login with `admin` as username

- To get the password, run

```
kubectl get secret -n argocd
```

![Image-2a](./Images/Image-2a.png)

Run `kubectl edit secret argocd-initial-admin-secret -n argocd`.

![Image-2b](./Images/Image-2b.png)

You will notice that the secret password is encrypted in base64. Copy the password and then decrypt it

```
echo Q3lKMTBRR0FMMjk5SWt3bQ== | base64 --decode
```

![Image-3a](./Images/Image-3.png)

Ignore the **%** character, copy and paste the decripted password to log into argocd.

![Image-3b](./Images/Image-3b.png)

3. Write simple [yml manifest (deployment , service)](./deploy/python-flask-app-manifests/deployment.yml) to deploy the python-app, push the code to your repo.

4. Write a Configuration file for argo CD to connect it to the git repository where the configuration files are hosted. Refer to[application[](./deploy/application.yml).

- `cd` to the directory containing the application.yml and apply this in the cluster.

```
kubectl apply -f application.yml
```

- Head back to your argo-cd UI browser to confirm that the python applicatiion has been created.

![Image-4](./Images/Image-4.png)

- Check that the pods are running as well

`kubectl get po -n tools`

![image-4b](./Images/Image-4b.png)

- Port forward to access the Python application from the browser

```
kubectl get svc
```

```
kubectl port-forward svc/python-service 8082:80
```

![Image-4c](./Images/Image-4c.png)

![Image-5](./Images/Image-5a.png)

![Image-5b](./Images/Image-5b.png)

![Image-5c](./Images/Image-5c.png)
