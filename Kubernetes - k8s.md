
# Main Components
--- 
Kubernetes is a container orchestration tool. It manages your containers and all their operations, making sure they conform to a specification provided by you (think scalability, connections and configurations).

Its main components are:
* **Pod**
	* Smallest unit in k8s;
	* A Pod is an abstraction over a container. It can have more than one container inside of it;
	* The name of a pod is composed of `deploymentName`-`replicaSetID`-`podID` (example `nginx-deployment-64594b9f56-czqdn`);
* **Deployment**
	* A deployment is an abstraction for managing different pods. In here, we declare:
		* The image to use for a pod;
		* The resources we want to allocate for a pod;
		* The scalability we want for that pod;
		* Others;
* **ConfigMap**
	* A list of key-value pairs that a Pod can use for configuring your application;
* **Secrets**
	* Akin to ConfigMap, but for sensitive values (like passwords);
	* Usually, we have Secret Manager services (Vault, GCP GSM) that are consumed by your k8s cluster (meaning K8S connects to these services to retrieve the value, it's not stored on the cluster itself);
* **ReplicaSet**
	* ReplicaSet manages how a pod is replicated;
* **Service**
	* Pods are given internal IPs on their generation. To not need to change the address of a pod every time a new one is created, the Service was created;
	* The service manages the connectivity to a deployment internally on a k8s cluster (it does not handle external accessibility);
* **Ingress**
	* Ingress is responsible for connecting to a service and making your app accessible externally;
* **Volume**
	* Pods are ephemeral, meaning that the data inside of it will not linger after it's destroyed;
	* Volumes can be used by pods to persist data between pod lifecycles;
* **StatefulSet**
	* StatefulSet are useful for stateful applications (like databases) so as to not lose data;
	* Usually, database are not managed by k8s (it's not easily doable) and instead is an external service that your k8s connects to;

# Architecture
--- 
Kubernetes is divided between **worker nodes** and **master nodes** (keep in mind you can have only one machine running both worker nodes and master nodes).

A **worker node** needs at least 3 processes:
* **Container Runtime***: responsible for actually running a container. It can be Docker or any other application for that;
* **Kubelet**: responsible for scheduling pods and containers. It acts as a interface layer between the host machine and the container runtime;
* **Kube Proxy**: manages connectivity in the worker node, forwarding requests (with routing logics) to pods, etc;

A **master node** needs at least 4 processes:
* **API Server***: acts as the cluster gateway. Responsible for authenticating and processing requests or queries in the cluster. Can be interacted via UI or CLIs as the entrypoint for the cluster;
* **Scheduler**: processes requests coming from API Server and decides (with different logics) appropriate worker nodes for the resources. Then, it sends instructions to the worker node's kubelet, which will actually schedule the resource creation;
* **Controller Manager**: responsible for keeping the cluster faithful to the desired state all the time, by detecting changes and recovering from broken states. Interacts with Scheduler to do so;
* **etcd**: key-value store for the cluster, acting as its brain. Every change on the cluster is updated in this "DB";

*"In practice, a k8s cluster is made of multiple master nodes where the API Server is load balanced"*

## Layers of Abstraction

A deployment **manages** => ReplicaSet **manages** => Pod is an abstraction of => **Containers**
## Kubectl

Kubectl is a command line for interacting with k8s cluster. Some useful commands:

```
$ kubectl get nodes
$ kubectl version
$ kubectl get [pods/services/replicaset]
$ kubectl create deployment nginx-depl --image=nginx
$ kubectl edit deployment nginx-depl
$ kubectl logs [pod-name]
$ kubectl describe pod [pod-name]
$ kubectl exec -it (interactive terminal) [pod-name] -- bin/bash
$ kubectl delete deployment [deploy-name]
$ kubectl apply -f [file_name] (example: kubectl apply -f config-file.yaml)
$ kubectl delete -f [file_name]
$ kubectl get pod -o wide
```

# Kubernetes Configuration
--- 
Although you can create and manage resources with only kubectl, you obviously will not do so. Usually, a cluster is managed and versioned in Git with configuration files. Every configuration file (YAML) has:
* A start point that tells what kind of resource is described in the file and the apiVersion of the resource;
* **Metada***;
* **Spec**: specific for every resource, contains different configurations;

Also, the resources have **status**, which is not on the file but is automatically generated and added by k8s. This information is stored in `etcd`.

Example of a configuration file:
```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec: # specification for deployment
  replicas: 2
  selector:
    matchLabels: # this informs k8s that this deployment manages any pod with this label
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec: # specification for pod 
      containers:
      - name: nginx
        image: nginx:1.16
        ports:
        - containerPort: 80
```

## Internal Communication

Internal communication on k8s is handled with labels and selectors:
* **Labels**: defined in the metadata section, is a bunch of key-value pairs that can be anything;
* **Selectors:** a selector uses
Communication works through labels and selectors. 
* Labels: defined in the metadata;
* Selectors: defined in the spec, instructs the resource on how to connect to its desired resource;
This part is a bit eerie, so let's get an example:
* Suppose you have a deployment and a service definition;
* Obviously, you want your service to forward requests to your pods. How is this reflected in your configuration files?

This is your deployment definition. Notice that we define that the pods (and the deployment) have a `app:nginx` label. More over, we are informing the deployment how to know which pods it is responsible for managing with the selector (that its using a `matchLabels` in this case).

``` YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16
        ports:
        - containerPort: 8080
```

Now let's go to the the service definition. The service also has a selector which is basically to say that it should connect to deployments that have the `app: nginx` label to forward its requests to.

```YAML
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx # will select any pods with "app: nginx" label to forward the request
  ports:
    - protocol: TCP
      port: 80 # port which the service itself is acessible through
      targetPort: 8080 # port to access in the POD
```

