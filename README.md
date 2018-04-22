Expermimenting with Kubernetes
===

A repo to experiment creating different environments

Examples include a basic nginx instance taken from the linux foundation example, kafka, and cassandra

Getting a spark and kafka instance to communicate to each other using resources taken from a few blogs linked below

## Resources used

[Linux Foundations Intro to Kubernetes Course](https://training.linuxfoundation.org/linux-courses/system-administration-training/introduction-to-kubernetes)

[Kubernetes Docs](https://kubernetes.io/docs/home/?path=browse)

[Bonzai Repos](https://github.com/banzaicloud/banzai-charts)

[Spinning up a Kafka cluster](http://dojoblog.dellemc.com/dojo/deploy-kafka-cluster-kubernetes/)

[Spinning up a Cassandra instance](https://kubernetes.io/docs/tutorials/stateful-application/cassandra/)

## Setup
Install kubectl and minikube if you don't have them already

Minikube is helpful for creating local kubernetes environments

Also by default minikube will only use 1gb of ram. Its not a bad idea to start minikube with more ram if you intend on experimenting with tools like cassandra

minikube start --memory int

## Following the linux foundation intro course 
You can use the below commands to set up an application environment

All yaml files from the below commands are in the BasicApplicaitonExample folder

```kubectl create -f rsvp-db.yaml``` 

```kubectl create -f rsvp-db-service.yaml```

```kubectl create -f rsvp-web.yaml```

Scale application from the command line to have additional nodes

```kubectl scale deployment rsvp --replicas=3```

List services

```kubectl get services```

List deployed items

```kubectl get deployments```

Open dashboard to manage kubernetes environment

```minikube dashboard```

Open the service that was spun up in prior steps

```minikube service rsvp```

## [Stateful sets](https://kubernetes.io/docs/tutorials/stateful-application/basic-stateful-set/)

Often used with stateful or distributed applications such as kafka

## Config maps
kubectl create configmap my-config --from-literal=key1=value1 

```kubectl get configmaps my-config -o yaml```

```kubectl create -f customer1-configmap.yaml```

## Secrets 
Secret data is stored as plain text inside etcd. Administrators should limit the access to the API server and etcd

```kubectl create secret generic my-password --from-literal=password=mysqlpassword```

Lists opaque doesn't actually show the contents of the password

```kubectl get secret my-password```

## Ingress 
An Ingress is a collection of rules that allow inbound connections to reach the cluster Services

```minikube addons enable ingress```

```kubectl create -f webserver-ingress.yaml```

## [Annotations](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/)

Helpful for info around deployments such as build/release IDs, PR numbers, git branch, etc.

## [Rollbacks](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-back-a-deployment)

TBD

## [Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)

Run from the Common Scenarios folder or explicitly specify the directory ```kubectl create -f cronjob.yaml```


## [Resource Quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/)
* Compute Resource Quota: Limit the total sum of compute resources (CPU, memory, etc.) that can be requested in a Namespace.
* Storage Resource Quota: Limit the total sum of storage resources (PersistentVolumeClaims, requests.storage, etc.)
* Object Count Quota: Restrict the number of objects of a given type (pods, ConfigMaps, PersistentVolumeClaims, ReplicationControllers, Services, Secrets, etc.).

## [DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)

```kubectl create -f daemonset.yaml```

Example: Add a node monitoring daemon on each node

## [Kubernetes Cluster Federation](https://kubernetes.io/docs/concepts/cluster-administration/federation/)

These allow for global DNS record. You can create hybrid solutions where you load balance between on premise data center and the cloud. You can also distribute workloads against each with different strategies

## [Charts](https://github.com/kubernetes/charts)

To deploy an application, we use different Kubernetes manifests, such as Deployments, Services, Volume Claims, Ingress, etc. We can bundle all those manifests after templatizing. These are called Charts and can then be served via repositories, such as those that we have for rpm and deb packages. 

## Middleware

Helm is a package manager like yum for Kubernetes, which can install/update/delete Charts in the Kubernetes cluster

Helm has two components:

* A client called helm, which runs on your user's workstation
* A server called tiller, which runs inside your Kubernetes cluster.

Heapster => A cluster wide aggregator of monitoring and event data

You can use tools like elastic search to aggregate the logs

## Zookeper
```kubectl create --f zookeeper.yaml```

## Kafka Instance
Run the kafka-service.yaml after the zookeper.yaml

Then you can start to interact with the kafka instance

bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test

## Cassandra

Headless service

```kubectl create -f cassandra-service.yaml```

Creating a cassandra ring

```kubectl create -f cassandra-statefulset.yaml```

If you are in the minikube dashboard you can click exec and run commands against cassandra

I found my instance didn't have python installed so if you want to use cqlsh you will need to install python

