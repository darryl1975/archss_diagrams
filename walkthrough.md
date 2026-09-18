### Architecting Software Solutions (Day 4) Demo was walkthrough

Step 1 Install RabbitMQ

https://www.rabbitmq.com/docs/download

# latest RabbitMQ 4.x
docker run -it --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:4-management

docker run -d \  
  --name rabbitmq-web-interface \  
  -p 5672:5672 \  
  -p 15672:15672 \  
  -e RABBITMQ_DEFAULT_USER=guest \  
  -e RABBITMQ_DEFAULT_PASS=guest \  
  rabbitmq:4-management 

## Run RabbitMQConsumer in Terminal/Command Prompt
MacOS: ./mvnw spring-boot:run
Windows: mvnw.cmd spring-boot:run

## Run RabbitMQPublisher in Terminal/Command Prompt
MacOS: ./mvnw spring-boot:run
Windows: mvnw.cmd spring-boot:run

## Test the consumer
use Postman send a GET http://localhost:8080/api-rabbitmq/producer?empName=Test&empId=12345
RabbitMQConsumer will receive and print message to console


### Kubernetes & ArgoCD (localhost)
Install Kubectl Kubectl is the Kubernetes command-line tool used to interact with your Kubernetes cluster.

brew install kubectl

Install minikube
https://minikube.sigs.k8s.io/docs/start/?arch=%2Fmacos%2Farm64%2Fstable%2Fbinary+download

brew install minikube


Start minikube

minikube start


Minikube will automatically detect and use a suitable driver (like Docker if it's running). Verify Minikube Status.

minikube status

use kubectl to interact with your local Kubernetes cluster provided by Minikube. For example, to check the cluster info:

kubectl cluster-info

Prepare the base project cicd-base (from my repo)


1. Create Namespace
kubectl create namespace argocd


2. Install Argo CD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

3. Change Service Type
kubectl patch svc argocd-server -p '{"spec": {"type": "NodePort"}}' -n argocd

Changing a Kubernetes Service type from ClusterIP to NodePort is done when external access to an application running within the cluster is required.

A ClusterIP Service provides a stable internal IP address for applications, allowing other Pods within the same Kubernetes cluster to communicate with it. It does not expose the application to traffic originating from outside the cluster.

4. Show Argo CD Resources
kubectl get po,svc,deployment -n argocd

5. Handle Argo CD Admin Password
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml


<you have your own password>
echo R3NaWmFQOExXLTN6aWRVZw== | base64 --decode

username: admin
password: PZPVcbDAhZCICpd2


6. Expose Argo CD Server
minikube service argocd-server --url -n argocd

* Visit http://127.0.0.1:<YOUR_PORT> and sign in your credentials.


create a k8s (or manifest) folder
create and put your springboot service.yaml and deployment.yaml into the folder

create argoCD application.yaml


Build Version 1 and Push Docker Image to Hub
sh build.sh

Deploy Spring Boot App With Argo CD
kubectl apply -f application.yaml


Test and check application is working and running
kubectl get svc -n cicd-demo-app  

kubectl port-forward svc/cicd-demo-app -n cicd-demo-app 8080
