# Containerisation topics

## Kubectl

minikube image build . -t skeleton-site:v1 -f .\SkeletonWebSite.Dockerfile
minikube kubectl -- create deployment skeleton-site --image=skeleton-site:v1


Exposing service
Note: port=<pod-port>   ; actual public/service port is decided by k8s
minikube kubectl -- expose deployment skeleton-site --type=LoadBalancer --port=80
minikube kubectl -- expose deployment skeleton-site --type=NodePort --port=80

minikube service skeleton-site --url

Manual scaling and Update
minikube kubectl -- scale deployment skeleton-site --replicas=3
minikube kubectl -- set image deployments skeleton-site skeleton-site=skeleton-site:v2

Autoscaling (hpa/horizontalpodautoscaler)
REMINDER: HPA seems to require(?) cpu and memory limits defined in the container section of deployments; 
          Else you might see `cpu: <unknown>/50%` under TARGETS when you watch hpa metrics
          Requests is what you ask for; Limit is hard limit
minikube addons enable metrics-server
minikube kubectl -- get apiservices
minikube kubectl -- autoscale deployment skeleton-site --cpu-percent=50 --min=1 --max=4
minikube kubectl -- get hpa
minikube kubectl -- get hpa skeleton-site --watch


minikube kubectl -- run -it load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh
minikube kubectl -- run -it load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://skeleton-site; done"
minikube kubectl -- run -it load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://10.106.57.175; done"


Ad-hoc pod-related commands

Run pod
minikube kubectl -- run test-skeleton-site --image=skeleton-site:v1
minikube kubectl -- run test-skeleton-site -it --rm --image=skeleton-site:v1

Labeling
kubectl label pods skeleton-site-74c76845fb-bqqh7 version=v1


Get pod/service of deployment
minikube kubectl -- get pod -l app=skeleton-site
minikube kubectl -- get pods -l app=skeleton-site,version=v1
minikube kubectl -- get pods -l app=skeleton-site -o custom-columns=POD_IP:.status.podIPs
minikube kubectl -- get service -l app=skeleton-site
minikube kubectl -- top pods

Open shell
minikube kubectl -- exec --stdin --tty test-skeleton-site -- /bin/bash
minikube kubectl -- exec -it test-skeleton-site -- /bin/bash


Config
kubectl config get-contexts
kubectl config set-context
kubectl config view

## To investigate 
- buildx?
    Install standalone? 
    Run?
 
100m CPU
100 milliCPU
0.1 CPU are all the same.



# Getting Docker
https://download.docker.com/win/static/stable/x86_64/
