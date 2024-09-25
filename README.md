# DevOps-Project-Bank-Application-Blue-Green-Deployment-Aws


Install Argo Rollout Controller
```
kubectl create namespace argo-rollouts
kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
```
<br><br/>
Install Argo Rollouts Kubectl plugin
```
curl -LO https://github.com/argoproj/argo-rollouts/releases/latest/download/kubectl-argo-rollouts-linux-amd64
chmod +x ./kubectl-argo-rollouts-linux-amd64
sudo mv ./kubectl-argo-rollouts-linux-amd64 /usr/local/bin/kubectl-argo-rollouts
```
<br><br/>
Finally Promote the rollout
```
kubectl get ro -n <namespace>
kubectl argo rollouts get rollout <rollout-name> -n <namespace>
kubectl argo rollouts promote <rollout-name> -n <namespace>
```
