# Kubernetes

---
## What is containerization? 
A container is a collection of software processes unifed by one namespace, with access to an operating system kernel that it shares with other containers and little to no access between containers.

A container is a runtime instance of a Docker image which contains three things
1. A docker image
2. An execution environment
3. A standard set of instructions

Eg: It's like classes and objects, where the containers is an object while the docker image is a class.

### Difference between VM and Container 
**VM**
1. One or many applications 
2. The necessary binaries and libraries that would exist on the OS
3. The entire guest os to interact with the applications. 

**Container**
1. Include the application and all of it's dependencies 
2. It will share the kernel with other containers
3. It is not tied to infrastructure -- it only needs docker engine installed on the host.
4. It will run as isolated processes in user space on the host os.

This allows containers to run on any computer, infrastructure or cloud.

### Advantages of containers
1. Applications are portable
2. Applications can be packaged in a standard way using containers.
3. Deployment is easy, fast and repeatable.
4. Testing packaging and integrations can be automated easily
5. Support new microservices architecture.
6. Reliable deployments
7. Consistent application lifecycle, can be configured once and run multiple times
8. Consistent environment for both devs and qa.
9. Help build a CICD pipleline (Continous Integration Continous Deployment)
10. It brings agility to your code.

---

## What is Kubernetes ?
Most popular container orchestrator.
It is designed to automate deploying, scaling, and operating applications containers. It's goal is to foster an ecosystem of components and tools that relive the burden of running applications in public and private clouds. 

Kubernetes is a platform to schedule and run containers on clusters of VM. It runs on bare metal, VMs, private datacenter and public cloud. 

Docker containers to develop and build applications, and then use K8s to run applications in your infrastructure. 

Orchestrator features 
1. Provision hosts
2. Instantiate containers on a host
3. Restart failing containers
4. Expose containers as services outside the cluster
5. Scale the cluster up or down.

---

## Kubernetes features

1. Multi-Host container scheduling : This feature is handled by the kube-scheduler which
- It assigns pods to nodes at runtime 
- Checks resource, quality of service, policies and user specifications before scheduling. 

2. Scalabilty and Availability
- Kubernetes master can be deployed in a highly available configuration
- Multi-region deployments are also available.

**Scalability**
- Supports 5000 node clusters
- 150,000 total pods
- Max of 100 pods per node
- Pods can be horizontally scaled via an API

3. Flexibility and Modularization
- Plug and play architecture
- Extend architecture when needed
- Add-ons: network drivers, service discovery, container runtime etc

**Two features that allow Kubernetes to scale are**
1. Registration 
2. Discovery

### Registration
Seamless nodes register themselves with master 

### Service Discovery
Automatic detection of services and enpoints via DNS or env variables.


4. Persistent Storage
- Much requested and important feature when working with containers. 
- Pods can use persistent volumes to store data. 
- Data retained across pod restarts and crashes. 

5. Application upgrades and downgrades - supported out of the box

6. Maintenance
- features are backward compatible 
- APIs are versioned
- Turn host off/on during maintence. 

7. Logging and Monitoring
- Application monitoring built in 
- Node health check
- Kubernetes status
- can use built in or external logging framework

8. Management of secrets
- Mounted as data volumes or env variables 
- Specific to namespace
- Sensitive data is first class citizen

---

## Major Players in Container Orchestration
- Kubernetes 
- Mesos 
    - Used larger enterprises 
    - Projects that require lots of compute
- Rancher 
    - Full stack container management platform
    - Install and manage Kubernetes clusters 
    - Great UI and API to interact with cluster

**When to go serverless ?** 
- Starting to build your infrastructure from scratch
- Running your infrastructure in the cloud

---
---

## Architecture of Kubernetes cluster 

`Master Node` : It is responsible for the overall management of the Kubernetes cluster. Its got three components that take care of communication, scheduling and controllers. 
- The API server : allows you to interact with the kubernetes API. It's the frontend of the Kubernetes control panel. 
- Scheduler : It watches created pods, who do not have a node assigned yet, and assigns the pod to run on a specific node.
- Controller Manager : It runs controllers, these are background threads that run tasks in a cluster. 

Then we have `etcd` which is a distributed key value store. Kubernetes uses etcd as it's database and stores all cluster data here like pod details, job scheduling info etc.

`kubectl` : you interact with the master node using kubectl application, which is the command line interface for K8s.

`Worker Nodes` : These are the nodes where your applications operate. It contains 
- kubelet
- kube-proxy
- Docker 
Worker nodes are exposed to the internet via load balancer. 

### Basic building blocks : Pods

`Pod` : It is the simplest unit that you can interact with. You can create, deploy, and delete pods, and it represents one running process on your cluster. 

It contains : 
- docker application container (one or more)
- storage resources
- unique network IP

States:
- Pending (Pod has been accepted by the system, but the container is not created yet)
- Running (Where pod is scheduled on the node, and all the containers are created and atleast one container is in running state)
- Succeeded (all containers in the pod have exited with exit status of 0)
- Failed (atleast one container has returned a non-zero exit status)
- CrashLoopBackOff (In this container fails to starts for some reason, and Kubernetes tries over and over to restart the pod)

### Deployments, jobs, and services: 

Benefits of controllers : 
- Application reliability
- Scaling 
- Load balancing 

Kinds of Controllers :
- ReplicaSets - Ensures that a specified number of replicas for a pod are running at all times.
- Deployments - it provides declarative updates for pod and replica set.
- Daemon Sets - It ensures that all nodes run a copy of a specific pod. (Log or monitoring tool)
- Jobs - Supervisor process for pods carrying out batch jobs. 
        Run individual processes that run once and complete successfully. 
- Services - Allow the communication between one set of deployments with another. We can use a service to get pods in two deployments to talk to each other. It provides a permanent IP to communicate otherwise, the pod IPs might change. 
    - Types of IPs
        - Internal - within the cluster 
        - External - endpoint available through node ip : port 
        - Load balancer  - expose your application to the public internet.

### Labels, selectors and namespaces 

`Labels` : Labels are key/value pairs that are attached to objects like pods, services, and deployments. Lables are users of Kubernetes to identify attributes for objects. 

eg : `"release" : "stable", "release" : "canary"`
    `"environment" : "dev", "environment" : "qa"`

`Selectors` : Label selectors allow you to identify a set of objects.
1. Equality Based 
    - "=" : Two labels or values of labels should be equal.
    - "!=" : Two labels or values should not be equal.
2. Set-Based 
    - IN : A value should be inside a set of defined values.
    - NOTIN : A value should not be in a set of defined values.
    - EXISTS : Determines whether a label exists or not.

`Namespaces` : it allows you to have multiple virtual clusters backed by same physical cluster. 
- Great for large enterprises
- Allows teams to access resources, with accountability.
- Great way to divide cluster resources between users.
- Provides scope for names - must be unique in the namespace. (services should have unique names in a same namespace but can be same in different namespaces)
- There is a "Default" namespace created when you launch kubernetes.
- Newer applications install their resources in a different namespace so they don't interfere with your existing cluster and cause confusion. 

### kubelet and kube proxy

`kubelet` :
- Communicates with API server to see if pods have been assigned to nodes. 
- Executes pod containers via a container engine. (Docker)
- Mounts and runs pod volumes and secrets
- Executes health checks to identify pod/node status and reports it back to API server.
- `Podspec` is a YAML file that describes a pod
- kubelet takes a set of podspecs that are provided by the kube-apiserver and ensures that the containers described in those podspecs are running and healthy.
- kubelet only manages containers that were created by the API server - not any other container running on the node.

`kube-proxy` :
- Process that runs on all worker nodes
- Reflects services as defined on each node, and can do simple network stream or round-robin forwarding across a set of backends. 
- Modes 
    - User space mode
    - Iptables mode
    - Ipvs mode (alpha feature)

**Why modes are important?**
- Services are defined against the API server : kube proxy watches the API server for addition and removal of services. 
- For each new service, kube-proxy opens a randomly chosen port on the local node. 
- Connections made to the chosen port are proxied to one of the corresponding back-end pods.

### Production ready 

So, you need to create yaml files which contains deployment, service, jobs etc and then you do 
`kubectl apply -f file.yaml`

**Health checks** : 
- ReadinessProbe detects when a container can accept traffic
- livenessProbe checks whether the container is alive and running ( you can have some sort of heartbeat API inside your application, which it would hit and check the status)
 you also set a failure threshold, that is that many heartbeats can be skipped before it crashes...and eventually goes into crashloopbackOff.

### Handling Application upgrades and rollback

You can upgrade the image via this command 

`kubectl set image deployment/my-deployment my-container=my-image:new-version`

To check deployment history:
`kubectl rollout history deployment/actionlib`

To revert : 
`kubectl rollout undo deployment/my-deployment --to-revision=version`

### To debug issues

To describe pods :
`kubectl describe pod pod-name`

To check logs for a particular pod : 
`kubectl logs pod-name`

To check logs of previous pod :
`kubectl logs pod-name -p`

To go inside a pod : 
`kubectl exec -it pod-name sh or /bin/bash`


### Dealing with configuration data

We can configure values, env variables at deploy time via configMap
`kubectl create configmap logger --from-literal=log_level=debug`

it would look something like this in yaml 
env : 
    - name: log_level
      valueFrom:
        configMapKeyRef:
            name: logger
            key: log_level

### Kubernetes secrets 

`kubectl create secret generic my-secret --from-literal=username=my-username --from-literal=password=mypassword`

you can also create secrets from files or yamls.
These values are base64 encoded 

How to use it in deployment : 

env :
    - name: api-key
        valueFrom:
            secretKeyRef:
                name: api-key
                key: api-key



### jobs in kubernetes 

To create a job:
`kubectl apply -f job.yaml`

To get jobs :
`kubectl get jobs | grep jobName`

To stop a cronjob, just edit the job and make suspendStatus = true

### Daemon sets in kubernetes 

Daemon set is a type of controller object that ensures that a copy of a specific pod runs on each node in the cluster. Unlike other controllers such as deployments or replicasets which ensure a specified number of replicas running in the cluster. Daemon sets are used when you need exactly one instance of a pod to run on each node in your cluster,

They are generally used for logging, monitoring, networking or loadbalancing etc

### Stateful set in kubernetes 

A stateful set in kubernetes is a workload API object used to manage stateful applications. While kubernets deployents are suitable for stateless applications, statefulsets are designed for applications taht require stable, unique network identifiers, stable storage and ordered graceful deployment and scaling. Examples of stateful applications include MySQL, postgresql, kafka etc

Each pod in a stateful set is assigned a stable network identifier (hostname)

To explain in short, it is used to deploy containerized mysql, postgresql or kafka in kubernetes. 

--- 
## Kubernetes deployment in production 

you can use kubeadm 
or kops for aws 


### Namespaces in kubernetes 

Kubernetes supports multiple virtual clusters backed by the same physical cluster. These virtual clusters are called namespaces. 

Usecases : 
- Roles and responsibilites in an enterprise 
- Partitioning landscapes : dev vs test vs prod
- customer partitioning for non-multi-tenant scenarios
- application partitioning

`kubectl get namespaces`
`kubectl -n namespacename get pods`

### Monitoring and logging 

- cAdvisor is an open-source resource usage collector that was built for containers. It auto discovers all containers in the given node and collects CPU memory filesystem, and network usage statistics. It provides the overall machine useage by analyzing the root container on the machine. 

- Prometheus is an open source systems monitoring and alerting toolkit. It was used to collect application metrics and monitor kubernetes via projects like kube-prometheus


these tools are typically linked to grafana, which is a data visualization platform.

For logging: 
Kubernetes will automatically pick up the logs and show you anything in the log info when you do, kubectl logs. 

Once your application is running in staging or production, you might consider using a logging platform like kibana with elasticSearch with logs being shippped to them from pods using fluentd or filebeat (logstash), to provide more robust search and analytics functionality.

Basic architecture: logshipper will collect logs from kubernetes and ship them to elastic search which is an analytics tool, and then kibana comes in for visualization and analysis.

### Authentication and Authorization 
Two kinds of users 
- normal users : Humans interacting with the system
- service accounts : Accounts managed by the K8s API

Information that defines a user:
- Username
- UID: an identifer that is more consistent or unique than username
- Group 

Authentication : does a user have access to the system ? 
Authorization : can the user perform an action in the system ? 

Popular Authentication Modules 
- Client certs 
- Static token files 
- OpenId connect 
- webhook mode 

Popular Authorization Modules 
- ABAC : Attribute-based access control 
- RBAC : Role-based access control. It sets permissions through rules associated with namespaces or clusters. It grants users access to kubernetes resources through the use of policies that combine roles.
- Webhook

eg of ABAC: 
All access : 
"apiVersion" : "abac.authorization.kubernetes.io/v1beta1", "kind":"Policy", "spec" : {"user" : "chaitu", "namespace":"\*", "resource":"\*", "apiGroup" :"\*"}

Read access : 
"apiVersion" : "abac.authorization.kubernetes.io/v1beta1", "kind":"Policy", "spec" : {"user" : "chaitu", "namespace":"\*", "resource":"\*", "apiGroup" :"\*", "readonly":true}
