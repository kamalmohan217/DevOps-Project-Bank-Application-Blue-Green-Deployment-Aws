# DevOps-Project-Bank-Application-Blue-Green-Deployment-Aws

![image](https://github.com/user-attachments/assets/5508fb2b-c141-43d1-89c6-a49c82df88ec)

![image](https://github.com/user-attachments/assets/0c90595a-eb50-4e42-99df-e024643ab516)


![image](https://github.com/user-attachments/assets/7c2c7ee0-d8d3-4714-ae36-db9630875927)


Installation of SonarQube-Scanner, Nexus Artifacts Uploader and pipeline utility step plugin as shown in the screenshot attached below.
![image](https://github.com/user-attachments/assets/5ad1a9bd-28ab-4032-944d-de3a97a546b2)

Steps to be followed for Installation of Ingress nginx controller, ArgoCD and ArgoCD CLI is as given below.
```
kubectl create ns ingress-nginx
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx

After creating it you need to edit the service and provide ssl certificate details and etc. in annotations as written below:- 
=================================================================
service.beta.kubernetes.io/aws-load-balancer-backend-protocol: http
service.beta.kubernetes.io/aws-load-balancer-connection-idle-timeout: "60"
service.beta.kubernetes.io/aws-load-balancer-cross-zone-load-balancing-enabled: "true"
service.beta.kubernetes.io/aws-load-balancer-ssl-cert: arn:aws:acm:us-east-2:XXXXXXXX:certificate/XXXXXX-XXXXXXX-XXXXXXX-XXXXXXXX
service.beta.kubernetes.io/aws-load-balancer-ssl-ports: https
service.beta.kubernetes.io/aws-load-balancer-type: elb

===================================================================
You need to change the targetPort for https to 80 in nginx ingress controller service as written below:-
-------------------------------------------------------------------------------------------------------------------------------
Before:

  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: http
    - name: https
      port: 443
      protocol: TCP
      targetPort: https
After:

  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: http
    - name: https
      port: 443
      protocol: TCP
      targetPort: 80
=================================================================

kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


You can get the password for ArgoCD Login using the command as given below

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d


Write the argocd-ingress-rule.yaml provide the service name and service port accordingly file as written below:-

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  namespace: argocd
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"    ###  You can use this option for this particular case for ArgoCD but not for all
#    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
#  ingressClassName: nginx
  rules:
  - host: argocd.singhritesh85.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: argocd-server   ### Provide your service Name
            port:
              number: 80     #### Provide your service port for this particular example you can also choose 443

----------------------------------------------
Installation of ArgoCD CLI
----------------------------------------------
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64

```

After Running the Jenkins Job the screenshot for SonarQube, Nexus and ArgoCD is as shown in the screenshot attached below.
![image](https://github.com/user-attachments/assets/f1aedb76-63b1-4168-ac3f-c64b7dc831e7)
![image](https://github.com/user-attachments/assets/1136b042-c059-472b-a26f-85654c212b0e)
![image](https://github.com/user-attachments/assets/17e28cd4-08e2-42cf-9046-016cce56772d)

The screenshot for two jenkins job is as shown below.
![image](https://github.com/user-attachments/assets/d6103aa1-6bdc-4ce2-aca5-a869c8e35139)

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

Entry for Route53 is as shown in the screenshot attached below.
![image](https://github.com/user-attachments/assets/21a54569-7798-4da0-bf46-982783a4548b)

We can access the Bank Application for the first time after running the jenkins job as shown in the screenshot attached below.
![image](https://github.com/user-attachments/assets/6e0ef5d4-f93f-41cf-95f9-aebc20b102af)
![image](https://github.com/user-attachments/assets/d7876d8c-a996-4325-8d13-8786d0532f41)

As per the requirement some changes has been done in the Application's login page (in the login.html file) and the bank name has been changed from Goldencat to GoldenDuck as a part of the new release.
For new release to get implemented the Jenkins Job has been run and as we can there are two service and pods for active and preview. The preview to get implemented first of all I check whether it is working properly or not. To do so I changed the service type of kubernetes from ClusterIP to LoadBalancer as shown in the screenshot attached below.
![image](https://github.com/user-attachments/assets/8d2eb926-66a2-44df-b0f9-4d1f36615b28)

I checked Bank Application is working properly as shown in the screenshot attached below.
![image](https://github.com/user-attachments/assets/aadbdbd2-4b7d-4b6e-ba66-427ad16dcb1a)

After checking make the kubernetes service type as ClusterIP as shown in the screenshot attached below.
![image](https://github.com/user-attachments/assets/60dd57eb-4718-46e3-b5ff-7b428fcfbed4)

Finally promote the rollout to make the changes as Active.
![image](https://github.com/user-attachments/assets/6a7386bc-0c22-4d72-bdcd-81115905e8a2)

![image](https://github.com/user-attachments/assets/48a46798-4000-4cb7-82a5-4f7e07f2acdb)
![image](https://github.com/user-attachments/assets/d7e390ec-332f-4927-99e9-ee759107d8da)

Access the Bank Application as shown in the screenshot attached below.
![image](https://github.com/user-attachments/assets/fced8338-6286-40a6-9af8-b0caa8a627c7)
![image](https://github.com/user-attachments/assets/de0e376d-4520-4dca-9123-aa99f42cac9b)


<br><br/>
<br><br/>
<br><br/>
<br><br/>
<br><br/>
<br><br/>
```
Source Code:-  https://github.com/kamalmohan217/Bank-App.git
Helm Chart:-   https://github.com/kamalmohan217/helm-repo-for-Blue-Green-Deployment.git
```
<br><br/>
<br><br/>
<br><br/>
<br><br/>
<br><br/>
<br><br/>
```
Reference:-    https://github.com/Goldencat98/Bank-App.git
```
