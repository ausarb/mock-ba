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

## Deployment
Go to a root directory.
Pull all the source repositories from github.
```
> cd c:/Users/matr00659/development/github/kubernetes
> git clone https://github.com/Mattersight/mock-ba.git
> git clone https://github.com/Mattersight/rabbit-mock-ba.git
> git clone https://github.com/Mattersight/mock-acd.git
> git clone https://github.com/Mattersight/mock-ti.git
> git clone https://github.com/Mattersight/mock-ae.git
> git clone https://github.com/Mattersight/mock-bi.git
```

Each repo has kubernetes files in a _\_k8s_ folder.
Apply all these file to a target kubernetes cluster.
First apply the files in mock-ba/_k8s
```
> kubectl apply -f mock-ba/_k8s/namespace.yml
> kubectl -n mock-ba apply -f mock-ba/_k8s/role.yml
> kubectl -n mock-ba apply -f mock-ba/_k8s/serviceaccount.yml
> kubectl -n mock-ba apply -f mock-ba/_k8s/clusterorlebinding.yml
> kubectl -n mock-ba apply -f mock-ba/_k8s/configmap.yml
> kubectl -n mock-ba apply -f mock-ba/_k8s/secret.yml
```
Then install the mock ba components.
```
> kubectl -n mock-ba apply -f rabbit_mock-ba/_k8s/service.yml
> kubectl -n mock-ba apply -f rabbit_mock-ba/_k8s/deployment.yml
```
```
> kubectl -n mock-ba apply -f mock-acd/_k8s/service.yml
> kubectl -n mock-ba apply -f mock-acd/_k8s/deployment.yml
```
```
> kubectl -n mock-ba apply -f mock-ti/_k8s/service.yml
> kubectl -n mock-ba apply -f mock-ti/_k8s/deployment.yml
```
```
> kubectl -n mock-ba apply -f mock-ae/_k8s/service.yml
> kubectl -n mock-ba apply -f mock-ae/_k8s/deployment.yml
```
```
> kubectl -n mock-ba apply -f mock-bi/_k8s/service.yml
> kubectl -n mock-ba apply -f mock-bi/_k8s/deployment.yml
```
