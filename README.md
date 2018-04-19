# mock-ba
Consists of the following components.

![image][diagram]

[diagram]: images/diagram.png "Diagram"

## mock-acd
Generates fake call event messages and publishes to a Rabbit exchange named _cti_.

## mock-ti
Subscribes to Rabbit exchange named _cti_, and receives the call event messages from mock-acd.
Publishes messages received from mock-acd to Rabbit queue named _ti_.

## mock-ae
Subscribes to Rabbit queue named _twitter_, and receives tweets from twitter.

Subscribes to Rabbit queue named _ti_, and receives the call event messages from mock-ti.
Generates a transcript from each message recieved from mock-ti.
Publishes transcript to Rabbit queue named _transcript_.

## mock-bi
Subscribes to Rabbit queue named _transcript_, and receives the transcripts from mock-ae.
Does nothing with the transcripts.

## rabbit-mock-ba
This is RabbitMQ.
This YAML creates an Ingress, hence there is a dependency on an Ingress Controller.
Ingress Controller is typically considered "infrastructure".
Only one is needed per cluster.
This has already been set up in our AWS kubernetes cluster.

## Setting up the Ingress Controller
Our AWS kubernetes cluster already has an Ingress Controller, so don't do this there.
However, on your minikube (or some other cluster you own/control) you will want to do this.

Go to your development root directory.
Pull the mock-ba-ingress-controller source repository from github.
> cd %USERPROFILE%/development/github/kubernetes  
> git clone https://github.com/Mattersight/mock-ba-ingress-controller.git

Apply all these file to your minikube cluster.
> kubectl apply -f mock-ba-ingress-controller/_k8s/namespace.yaml  
> kubectl apply -f mock-ba-ingress-controller/_k8s/without-rbac.yaml  
> kubectl apply -f mock-ba-ingress-controller/_k8s/service-nodeport.yaml  
> kubectl apply -f mock-ba-ingress-controller/_k8s/default-backend.yaml  
> kubectl apply -f mock-ba-ingress-controller/_k8s/tcp-services-configmap.yaml  
> kubectl apply -f mock-ba-ingress-controller/_k8s/udp-services-configmap.yaml  

Note that these kubectl commands don't specify a namespace.
This is because the YAML files specify the namespace.

## Deployment of the mock-ba components
Go to a root directory.
Pull all the source repositories from github.
> cd %USERPROFILE%/development/github/kubernetes  
> git clone https://github.com/Mattersight/mock-ba.git  
> git clone https://github.com/Mattersight/rabbit-mock-ba.git  
> git clone https://github.com/Mattersight/mock-acd.git  
> git clone https://github.com/Mattersight/mock-ti.git  
> git clone https://github.com/Mattersight/mock-ae.git  
> git clone https://github.com/Mattersight/mock-bi.git  

Each repo has kubernetes files in a _\_k8s_ folder.
Apply all these file to a target kubernetes cluster.
First apply the files in mock-ba/_k8s
> kubectl apply -f mock-ba/_k8s/namespace.yml  
> kubectl apply -f mock-ba/_k8s/configmap.yaml  
>    
> kubectl -n mock-ba apply -f mock-ba/_k8s/role.yml  
> kubectl -n mock-ba apply -f mock-ba/_k8s/serviceaccount.yml  
> kubectl -n mock-ba apply -f mock-ba/_k8s/clusterrolebinding.yml  
> kubectl -n mock-ba apply -f mock-ba/_k8s/configmap.yml  
> kubectl -n mock-ba apply -f mock-ba/_k8s/secret.yml  

Then install the mock ba components.
> kubectl -n mock-ba apply -f rabbit-mock-ba/_k8s/service.yml  
> kubectl -n mock-ba apply -f rabbit-mock-ba/_k8s/deployment.yml  
> kubectl -n mock-ba apply -f rabbit-mock-ba/_k8s/ingress.yml  
>      
> kubectl -n mock-ba apply -f mock-acd/_k8s/service.yml  
> kubectl -n mock-ba apply -f mock-acd/_k8s/deployment.yml  
>   
> kubectl -n mock-ba apply -f mock-ti/_k8s/service.yml  
> kubectl -n mock-ba apply -f mock-ti/_k8s/deployment.yml  
>    
> kubectl -n mock-ba apply -f mock-ae/_k8s/service.yml  
> kubectl -n mock-ba apply -f mock-ae/_k8s/deployment.yml  
>    
> kubectl -n mock-ba apply -f mock-bi/_k8s/service.yml  
> kubectl -n mock-ba apply -f mock-bi/_k8s/deployment.yml  
