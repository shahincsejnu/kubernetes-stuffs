# kubernetes-stuffs 

## Description

Collection of the resources sequentially for learning kubernetes. 

## Kubernetes

Kubernetes is container orchestration tools. It offers three most important facilities :

- High Availability or No Downtime
- Scalability or High Performance
- Disaster Recovery - Backup and Restore 

## Instruction for using this repo

At first fork this repo, then go through the resources sequentially as they are given. Everytime when you complete a tutorial tick the checkbox by giving `x` in the `- [ ]` eg. `- [x]` and then push that to your forked repo, by this way you can track your progress.

## Installation

- [x] Install [minikube](https://minikube.sigs.k8s.io/docs/start/)
    - Minikube is a lightweight Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node. 
- [x] Install [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
    - The kubectl command line tool lets you control Kubernetes clusters. kubectl is kubernetes command line interface which uses the kubernetes API to interact with the cluster.

## Commands

### Step #01: Create a kubernetes cluster

|Commands | Uses|
|---------|-------|
|`minikube version`|Check that minikube is properly installed|
|`minikube start`|To run kubernetes cluster. You now have a running Kubernetes cluster in your online terminal. Minikube started a virtual machine for you, and a Kubernetes cluster is now running in that VM.|
|`minikube ip`| To see the IP of the VM|
|`kubectl version`|To check if kubectl is installed properly. kubectl is configured and we can see both the version of the client and as well as the server. The client version is the kubectl version; the server version is the Kubernetes version installed on the master. You can also see details about the build.|
|`kubectl cluster-info`|To view the cluster details|
|`kubectl get nodes`|To view the nodes in the cluster. This command shows all nodes that can be used to host our applications. Now we have only one node, and we can see that its status is ready (it is ready to accept applications for deployment).|
|`minikube stop`| To halt the cluster|
|`minikube pause`| Pause kubernetes without impacting deployed applications|
|`minikube delete --all`| To delete all of the minikube clusters|

### Step #02: Deploying an App on kubernetes using kubectl

|Commands | Uses|
|-----------|--------|
|`kubectl create deployment <deployment_name> <app_image_location>`| To deploy your app on kubernetes. Note that, nclude the full repository url for images hosted outside Docker hub but for images from docker hube just give the repo/image_name| 
|`kubectl create deployment restapi shahincsejnu/httpapiserver:v1.0.7`| T deploy httpapiserver image on kubernetes|
|`kubectl get deployment`| To see the list of all your deployments|
|`kubectl proxy`| Pods that are running inside Kubernetes are running on a private, isolated network. By default they are visible from other pods and services within the same kubernetes cluster, but not outside that network. When we use kubectl, we're interacting through an API endpoint to communicate with our application. We now have a connection between our host (the online terminal) and the Kubernetes cluster. The proxy enables direct access to the API from these terminals. | 
|`curl http://localhost:8001/`| When the kube-proxy is running to see all the endpoints in kube api server|
|`curl http://localhost:8001/version`| To request an endpoint of the api server, give your desired endpoint instead version|

### Step #03: Explore the App : Viewing Pods and Nodes

|Commands | Uses|
|---------|-------|
|`kubectl get <nodes/pods/deployments/services>`| To see the List of all related(nodes/pods/deployments) resources|
|`kubectl describe <nodes/pods/deployments>`| To show detailed information about a resource|
|`kubectl logs $POD_NAME`| To print the logs from a container in a pod. Note: We don’t need to specify the container name, because we only have one container inside the pod.|
|`kubectl exec`| To execute a command on a container in a pod|
|`kubectl exec $POD_NAME env`| To view the list of environment variables of the pod|
|`kubectl exec -ti $POD_NAME bash`| To start a bash session in the pod's container|
|`exit`| To close your container connection, from inside the container|

### Step #04: Expose you app publicly : using a service to expose your app

|Commands | Uses|
|---------|-------|
|`kubectl get services`| To see the list of services. Note that, Although each Pod has a unique IP address, those IPs are not exposed outside the cluster without a Service. Services allow your applications to receive traffic.|
|`kubectl deployment/<deployment_name> --type="NodePort" --port 8080`| o create a new service and expose it to external traffic we’ll use the expose command with NodePort as parameter (minikube does not support the LoadBalancer option yet).|
|`kubectl describe services/<service_name>`| To find out what port was opened externally (by the NodePort option) |
|`curl $(minikube ip):$NODE_PORT`| Now we can test that the app is exposed outside of the cluster using curl, the IP of the Node and the externally exposed port:|
|`curl $(minikube ip):$NODE_PORT/api/articles`| |
|`kubectl describe deployment`| The Deployment created automatically a label for our Pod. With describe deployment command you can see the name of the label:|
|`kubectl get pods -l <label_values>`| |
|`kubectl get pods -l run=kubernetes-bootcamp`| Let’s use this label to query our list of Pods. We’ll use the kubectl get pods command with -l as a parameter, followed by the label values:|
|`kubectl get services -l run=kubernetes-bootcamp`| we can do same to list the existing services|
|`kubectl label pod $POD_NAME <new_label>`| To apply a new label we use the label command followed by the object type, object name and the new label. This will apply a new label to our Pod (we pinned the application version to the Pod), and we can check it with the describe pod command: |
|`kubectl describe pods $POD_NAME`| We see here that the label is attached now to our Pod. And we can query now the list of pods using the new label:|
|`kubectl get pods -l <new_lable>`| |
|`kubectl delete service -l <label_name>`| To delete Services you can use the delete service command. Labels can be used also here:|
|`kubectl get services`| Confirm that service is gone|
|`curl $(minikube ip):$NODE_PORT`| This confirms that our Service was removed. To confirm that route is not exposed anymore you can curl the previously exposed IP and port: We see here that the application is up. This is because the Deployment is managing the application. To shut down the application, you would need to delete the Deployment as well.|
|`kubectl exec -ti $POD_NAME curl localhost:8080`| You can confirm that the app is still running with a curl inside the pod:|



## Resources

- [Kubernetes Basics Modules](https://kubernetes.io/docs/tutorials/kubernetes-basics/) 
    - [x] Create a Kubernetes cluster
    - [x] Deploy an app
    - [x] Explore your app
    - [x] Expose you app locally
    - [x] Scale up your app
    - [x] Update your app
    
- [ ] Install `minikube` and run all six kubernetes basics modules in locally
    
- Video Tutorials from Youtube
    - [x] [Kubernetes Architecture Simplified | K8s Explained in 10 Minutes](https://www.youtube.com/watch?v=8C_SCDbUJTg&pbjreload=101)
    - [x] [Kubernetes Concepts Explained in 9 minutes!](https://www.youtube.com/watch?v=QJ4fODH6DXI)
    - [x] [What is Kubernetes | Kubernetes explained in 15 mins](https://www.youtube.com/watch?v=VnvRFRk_51k&list=PLy7NrYWoggjziYQIDorlXjTvvwweTYoNC)
    - [x] [Monolithic Architecture](https://www.youtube.com/watch?v=MPpU8y3ykS4&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=2)
    - [x] [Microservices Architecture](https://www.youtube.com/watch?v=2CAU4xWdKVM&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=3)
    - [x] [What are Containers?](https://www.youtube.com/watch?v=0aNdTsNdA8E&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=4)
    - [x] [Physical Servers vs. VM vs. Containers](https://www.youtube.com/watch?v=dq3SpOGDk60&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=5)
    - [x] [What is Docker? ](https://www.youtube.com/watch?v=2-G6tw0sULs&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=6)
    - [x] [Container Orchestration Engine (COE)](https://www.youtube.com/watch?v=mrhrsLRRqcM&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=7)
    - [x] [TOP 3 Container Orchestration Engines in Kubernetes ](https://www.youtube.com/watch?v=SKxJp9XEL6g&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=8)
    - [x] [What is Kubernetes?](https://www.youtube.com/watch?v=LWq7dg3Yb40&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=9)
    - [x] [Kubernetes Architecture Made Easy](https://www.youtube.com/watch?v=_3NUI5vasPk&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=10)
    - [ ] [Installation Methods of Kubernetes](https://www.youtube.com/watch?v=HwVELhvL8uA&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=11)
    - [ ] [Kubectl in Kubernetes](https://www.youtube.com/watch?v=5jo6gNGcs3s&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=12)
    - [ ] [Play-With-K8s (PWK) in Kubernetes](https://www.youtube.com/watch?v=YAGIEyNJ3QU&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=13)
    - [ ] [Minikube in Kubernetes](https://www.youtube.com/watch?v=U9BF6EpFpDc&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=14)
    - [x] [Kubernetes Components explained! Pods, Services, Secrets, ConfigMap](https://www.youtube.com/watch?v=Krpb44XR0bk&list=PLy7NrYWoggjziYQIDorlXjTvvwweTYoNC&index=2)
    - [x] [Pods and Containers - Kubernetes Networking](https://www.youtube.com/watch?v=5cNrTU6o3Fw)
    
- Full Course Videos
    - [ ] [Kubernetes Tutorial for Beginners [FULL COURSE in 4 Hours]](https://www.youtube.com/watch?v=X48VuDVv0do&t=2089s)
    
    
### Kubenetes Concepts (collected from [Alif](https://github.com/spectro30))
1. Videos
    - [ ] What is Kubernetes: https://www.youtube.com/watch?v=VnvRFRk_51k
    - [ ] Kubernetes Architecture:
        - [ ] https://www.youtube.com/watch?v=umXEmn3cMWY
        - [ ] https://www.youtube.com/watch?v=8C_SCDbUJTg
        - [ ] https://www.youtube.com/watch?v=_3NUI5vasPk&t=780s
    - [ ] Kubernetes Pods and Containers:
        - [ ] https://www.youtube.com/watch?v=5cNrTU6o3Fw
        - [ ] https://www.youtube.com/watch?v=6uvHVVNq34w&t=983s
    - [ ] Kubernetes Configuration(YAML) File:
        - [ ] https://www.youtube.com/watch?v=qmDzcu5uY1I&t=2s
    - [ ] Kubernetes Services:
        - [ ] https://www.youtube.com/watch?v=5lzUpDtmWgM
        - [ ] https://www.youtube.com/watch?v=J30_ZdaEXbw
            - [ ] https://www.youtube.com/watch?v=eth7osiCryc (NodePort)
            - [ ] https://www.youtube.com/watch?v=dVDElh_Kd48&t=25s(ClusterIP)
        - [ ] Ingress:
            - [ ] https://www.youtube.com/watch?v=80Ew_fsV4rM&t=671s
            - [ ] https://www.youtube.com/watch?v=VicH6KojwCI
        - [ ] EndPoint Slices:
            - [ ] https://nigelpoulton.com/blog/f/kubernetes-endpoint-slices-explained
    - [ ] Controllers:
        - [ ] https://www.youtube.com/playlist?list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ (Watch the videos on following topics from this playlist -  ReplicaSet, ReplicationController, Deployments, DaemonSet, Jobs)
        - [ ] https://www.youtube.com/watch?v=pPQKAR1pA9U (StatefulSet)
    - [ ] Kubernetes Namespaces: 
        - [ ] https://www.youtube.com/watch?v=VnvRFRk_51k
    - [ ] Storage
        - [ ] https://www.youtube.com/watch?v=0swOh5C3OVM (Volume, PV and PVC)
        - [ ] https://www.youtube.com/watch?v=TnfvE8o9wmg ( Volume)
        - [ ] https://www.youtube.com/watch?v=x2sMWUkasoE&t=10s (PV and PVC)
    - [ ] ConfigMap and Secret: 
        - [ ] https://www.youtube.com/watch?v=FAnQTgr04mU
        
2. Topics
    - [ ] Overview 
        - [ ] What is Kubernetes
        - [ ] Kubernetes Components
        - [ ] The Kubernetes API
        - [ ] Working with Kubernetes Objects
            - [ ] Understanding Kubernetes Objects
            - [ ] Kubernetes Object Management
            - [ ] Object Names and IDs
            - [ ] Namespaces
            - [ ] Labels and Selectors
            - [ ] Annotations
            - [ ] Field Selectors
            - [ ] Recommended Labels
    - [ ] Cluster Architecture
        - [ ] Nodes
        - [ ] Control Panel-Node Communication
        - [ ] Controllers
        - [ ] Cloud Controller Manager
    - [ ] Containers
        - [ ] Images
        - [ ] Container Environments
        - [ ] Runtime Class
        - [ ] Container Lifecycle Hooks
    - [ ] Workloads
        - [ ] Pods
            - [ ] Pod Lifecycle
            - [ ] Init Containers
            - [ ] Pod Topology Spread Constraints
            - [ ] Pod Presets
            - [ ] Disruptions
            - [ ] Ephemeral Containers
        - [ ] Controllers
            - [ ] ReplicaSet
            - [ ] ReplicationController
            - [ ] Deployments
            - [ ] StatefulSets
            - [ ] DaemonSet
            - [ ] Jobs
            - [ ] Garbage Collection
            - [ ] TTL Controller for Finished Resources
            - [ ] CronJob
    - [ ] Services, Load Balancing, and Networking
        - [ ] Service
        - [ ] Service Topology
        - [ ] EndpointSlices
        - [ ] DNS for Services and Pods
        - [ ] Connecting Applications with Services
        - [ ] Ingress
        - [ ] Ingress Controllers
        - [ ] Network Policies
        - [ ] Adding entries to Pos/etc/hosts with HostAliases
        - [ ] IPv4/IPv6 dual-stack
    - [ ] Storage
        - [ ] Volumes
        - [ ] Persistent Volumes
        - [ ] Volume Snapshots
        - [ ] CSI Volume Cloning
        - [ ] Storage Classes
        - [ ] Volume Snapshot Classes
        - [ ] Dynamic Volume Provisioning
        - [ ] Node-specific Volume Limits
    - [ ] Configuration
        - [ ] Configuration Best Practices
        - [ ] ConfigMaps
        - [ ] Secrets
        - [ ] Managing Resources for Containers
        - [ ] Pod Overhead
        - [ ] Resources Bin Packing for Extended Resources
        - [ ] Organizing Cluster Access Using kubeconfig Files
        - [ ] Pod Priority and Preemption
    - [ ] Security
        - [ ] Overview of Cloud Native Security
        - [ ] Pod Security Standards
    - [ ] Policies
        - [ ] Limit Ranges
        - [ ] Resource Quotas
        - [ ] Pod Security Policies
    - [ ] Scheduling and Eviction
        - [ ] Kubernetes Scheduler
        - [ ] Taints and Tolerations
        - [ ] Assigning Pods to Nodes
        - [ ] Scheduling Framework
        - [ ] Scheduler Performance Tuning
    - [ ] Cluster Administration
    - [ ] Extending Kubernetes
        - [ ] Volumes  - 10
        - [ ] Persistent Volumes 11 
        - [ ] Volume Snapshots 12 
        - [ ] CSI Volume Cloning 01
        - [ ] Storage Classes 02
        - [ ] Volume Snapshot Classes 03
        - [ ] Dynamic Volume Provisioning 04
        - [ ] Node-specific Volume Limits 05

    
