# How to install and setup Ingress-Nginx Controller 
###Setp by Step Guide

###Step 1: Configure Metallb load balancer 
- To install MetalLB, apply the manifest:
```bash
    kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.5/config/manifests/metallb-native.yaml
```
- Defining The IPs To Assign To The Load Balancer Services
###First you need to identify your node IP :
```bash
    kubectl get node -o wide
```
###In order to assign an IP to the services, MetalLB must be instructed to do so via the IPAddressPool CR.
###All the IPs allocated via IPAddressPools contribute to the pool of IPs that MetalLB uses to assign IPs to services.
- vim metal-lb-config.yml
```bash
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system
spec:
  addresses:
  - 172.18.0.1-172.18.0.5
  - fc00:f853:0ccd:e799::/124
```
### Apply the the manifest
- kubectl apply -f metal-lb-config.yml
-  MetalLB Installtion Link <br>
    - Reference: <b> <a href="https://metallb.universe.tf/installation/"><u> MetalLB Installation</a></u></b>
-  MetalLB Configuration Link <br>
    - Reference: <b> <a href="https://metallb.universe.tf/configuration/"><u> MetalLB IP Configuration </a></u></b>


###Step 2: Install the Nginx Ingress Controller 
- Apply the the manifest for Bare metal clusters
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.1/deploy/static/provider/baremetal/deploy.yaml
```

- Check the configuration of Ingress-Nginx controller
```bash
kubectl -n ingress-nginx get all
```
- To Change the Service from NodePort to LoadBalancer, edit the ingress-nginx-controller service
```bash
kubectl -n ingress-nginx edit svc ingress-nginx-controller
```
###To make sure service is shfited from NodePort to LoadBalancer
```bash
kubectl -n ingress-nginx get all
```
-  Bare Metal Ingress-Nginx Controller Link <br>
    - Reference: <b> <a href="https://kubernetes.github.io/ingress-nginx/deploy/#bare-metal-clusters"><u> Ingress-Nginx Controller </a></u></b>

###Step 3: Apply the Nginx Ingress Resource
- Ingress-Nginx Ressource Template Link <br>
    - Reference: <b> <a href="https://docs.nginx.com/nginx-ingress-controller/configuration/ingress-resources/basic-configuration/"><u> Ingress-Nginx Ressource Template </a></u></b>
    
###make sure to populate the correct ingress-nginx class name
```bash
kubectl -n ingress-nginx get ingressclass
```
###to check the ingress resource 

```bash
kubectl -n app-space get ingress
```
- To see the more detail about ingress
```bash
kubectl -n app-space desribe ingress
```
- To see the  detail about ingress service
```bash
kubectl -n ingress-nginx get svc 
```
- If, you are using Kind Cluster you need to port forward
 ```bash
kubectl port-forward -n ingress-nginx service/ingress-nginx-controller 8080:80
```
- To check and confirm, Whatever individual Deployment and service is wokring fine using ClusterIP under kind cluster
 ```bash
kubectl get node
###then enter the
docker exec -it prod-worker sh
###Curl <Service IP>:Port
```
###Note: Customize the ingress resource manifest as per your requirements
