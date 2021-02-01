# kubernetes-stuffs 

## Description

Collection of the resources sequentially for learning kubernetes. 

## Kubernetes

Kubernetes is a container orchestration tool. It offers three most important facilities :

- High Availability or No Downtime
- Scalability or High Performance
- Disaster Recovery - Backup and Restore 

## Instruction for using this repo

At first fork this repo, then go through the resources sequentially as they are given. Everytime when you complete a tutorial tick the checkbox by giving `x` in the `- [ ]` eg. `- [x]` and then push that to your forked repo, by this way you can track your progress.

## [Cheat Sheet of Kubernetes](https://github.com/dennyzhang/cheatsheet-kubernetes-A4)

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
|`minikube start`|To run kubernetes cluster. You now have a running Kubernetes cluster. Minikube started a virtual machine for you, and a Kubernetes cluster is now running in that VM.|
|`minikube ip`| To see the IP of the VM|
|`minikube dashboard`| To see the dashboard|
|`kubectl version`|To check if kubectl is installed properly. kubectl is configured and we can see both the version of the client and as well as the server. The client version is the kubectl version; the server version is the Kubernetes version installed on the master. You can also see details about the build.|
|`kubectl cluster-info`|To view the cluster details|
|`kubectl get nodes`|To view the nodes in the cluster. This command shows all nodes that can be used to host our applications. Now we have only one node, and we can see that its status is ready (it is ready to accept applications for deployment).|
|`kubectl get all`| To list all applications and services on the cluster|
|`minikube stop`| To halt the cluster|
|`minikube pause`| Pause kubernetes without impacting deployed applications|
|`minikube delete --all`| To delete all of the minikube clusters|
|`minikube delete`| To delete the minikube VM|
|`minikube addons list`| To see the list of the currently supported addons in minikube|
|`minikube addons enable <addons_name>`| To enable an addon|
|`minikube addons disable <addons_name>`| To disable an addon|


### Step #02: Deploying an App on kubernetes using kubectl

|Commands | Uses|
|-----------|--------|
|`kubectl create deployment <deployment_name> --image=<app_image_location>`| To deploy the app on kubernetes. Include the full repository url for images hosted outside Docker hub but for images from docker hube just give the repo/image_name:tag| 
|`kubectl create deployment restapi --image=shahincsejnu/httpapiserver:v1.0.7`| To deploy httpapiserver:v1.0.7 image on kubernetes from dockerhub with username shahincsejnu|
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
|`kubectl exec -ti $POD_NAME <bash or sh>`| To start a bash session in the pod's container|
|`exit`| To close your container connection, from inside the container|

### Step #04: Expose you app publicly : using a service to expose your app

|Commands | Uses|
|---------|-------|
|`kubectl get services`| To see the list of services. Note that, Although each Pod has a unique IP address, those IPs are not exposed outside the cluster without a Service. Services allow your applications to receive traffic.|
|`kubectl deployment/<deployment_name> --type="NodePort" --port 8080`| o create a new service and expose it to external traffic we’ll use the expose command with NodePort as parameter (minikube does not support the LoadBalancer option yet).|
|`kubectl describe services/<service_name>`| To find out what port was opened externally (by the NodePort option) |
|`curl $(minikube ip):$NODE_PORT`| Now we can test that the app is exposed outside of the cluster using curl, the IP of the Node and the externally exposed port:|
|`curl $(minikube ip):$NODE_PORT/api/articles`| |
|`curl -X GET --user admin:admin $(minikube ip):$NODE_PORT/api/login`| |
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

### Step #05: Scale your app : Running multiple instances of your app

|Command | Uses|
|-------|-------|
|`kubectl get rs`| To see the ReplicaSet created by the Deployment. Notice that the name of the ReplicaSet is always formatted as [DEPLOYMENT-NAME]-[RANDOM-STRING]. The random string is randomly generated and uses the pod-template-hash as a seed.|
|`kubectl scale deployments/<deployment_name> --replicas=4`| To scale up we'll use the kubectl scale command, followed by the deployment type, name and desired number of instances|
|`kubectl get deployments`| Now see the deployment list|
|`kubectl get pods -o wide`| Next, let’s check if the number of Pods changed. There are 4 Pods now, with different IP addresses.|
|`kubectl describe deployments/<deployment_name>`| The change was registered in the Deployment events log. To check that, use the describe command|
|`kubectl describe services/<service_name>`| To find out the exposed IP and Port we can use the describe service|
|`curl $(minikube ip):$NODE_PORT`| Next, we’ll do a curl to the exposed IP and port. Execute the command multiple times. We hit a different Pod with every request. This demonstrates that the load-balancing is working.|
|`kubectl scale deployments/<deployment_name> --replicas=2`| To scale down the Service to 2 replicas, run again the scale command.|
|`kubectl get deployments`| List the Deployments to check if the change was applied with the get deployments command.|
|`kubectl get pods -o wide`| The number of replicas decreased to 2. List the number of Pods, with get pods: This confirms that 2 Pods were terminated.|

### Step #06: Update your app : Performing a Rolling update

|Command | Uses|
|--------|------|
|`kubectl get deployments`| To list all of the deployments|
|`kubectl get pods`| To list the running pods|
|`kubectl describe pods`| To view the current image version of the app, look at the image field|
|`kubectl set image deployments/<deployment_name> <new_image_version>`| To update the image of the application to version 2, use the set image command, followed by the deployment name and the new image version. The command notified the Deployment to use a different image for your app and initiated a rolling update.|
|`kubectl get pods`| Check the status of the new Pods, and view the old one.|
|`kubectl describe services/<service_name>`| Verify and update. First, let’s check that the App is running. To find out the exposed IP and Port we can use describe service.|
|`curl $(minikube ip):$NODE_PORT`|  we’ll do a curl to the the exposed IP and port. We hit a different Pod with every request and we see that all Pods are running the latest version (v2).|
|`kubectl rollout status deployments/<deployment_name>`| The update can be confirmed also by running a rollout status command.|
|`kubectl describe pods`| To view the current image version of the app, run a describe command against the Pods. We run now version 2 of the app (look at the image field).|
|`kubectl rollout undo deployments/<deployment_name>`| If there is no image in the repository that we deployed then we do roll back. The rollout command reverted the deployment to the previous known state (v2 of the image). Updates are versioned and you can revert to any previously know state of a Deployment.|
|`kubectl get pods`| List again the pods|
|`kubectl describe pods`| Check again the image deployed on them| 


### Some intuition

- `docker ps` to see the container where minikube image is running
- `docker exec -it <minikube_container_id>` to enter the VM/container of the min
- again do `docker ps` to see the all docker containers that are running on minikube container
- now you can enter any specific container by doing exec and see the things.
- `kubectl describe pods <pod_name>` for seeing the specific pod information
- `watch -n 1 kubectl get pods` to see the pods with updating every other second
- `kubectl get pod <pod_name> -o yaml` to see the details of pod configuration yaml format

### Deleting an application/deployment with kubectl

- `kubectl get all` 
- `kubectl delete <deployment_name> <deployment_related_service_name>`
- or `kubectl delete deployment deployment_name` and then `kubectl delete servcie servcie_name`

### kubectl completion for fish shell

- `mkdir -p ~/.config/fish/completions`
- `cd ~/.config/fish`
- `git clone https://github.com/evanlucas/fish-kubectl-completions`
- `ln -s ../fish-kubectl-completions/completions/kubectl.fish completions/`

### kubectl port-forward : Forward a local port to a port on the Pod

- Kubectl port-forward allows you to access and interact with internal Kubernetes cluster processes from your localhost. You can use this method to investigate issues and adjust your services locally without the need to expose them beforehand. 
    - `kubectl port-forward pod_name pod_port:local_port`


## kubernetes tasks using [kind](https://kind.sigs.k8s.io/docs/user/quick-start/)

### Some important commands of kind

- `cat ~/.kube/config` to print the configuration of kubernetes for the clusters
- `kubectl config view` to see the clusters and their server and also current cluster
- `kubectl config use-context kind-<cluster_name>` to switch to <cluster_name> cluster
- `kubectl cluster-info --context kind-<cluster_name>` to see the <cluster_name> cluster info
- `kind create cluster` to create a new cluster and also current cluster become this new cluster
- `kubectl get pods --all-namespaces -o wide`
- `kubectl get node -o wide` to see the details of node with node ip, for using `curl -X GET --user admin:admin http://<node_internal_ip>:<node_exposed_port>/api/login`
- `kubectl get deployment/pod/service <deployment/pod/service_name> -o yaml` to see the deployment/pod/service configuration of <deployment/pod/service_name> in yaml format
- `kubectl get deployment/pod/service <deployment/pod/service_name> -o yaml > <file_name>.yaml` to see the deployment/pod/service configuration of <deployment/pod/service_name> in yaml format and save it to a yaml file in the current directory.
- `kubectl get pods --show-lables` to show the pods with their label names

### deployment and service through yaml

- `kubectl apply -f <deployment/service_file_name>.yaml` to create a deployment/service
- `kubectl apply -k <directory/folder_where_the_yaml_exists>` to create all the yaml in one command 


### K8s API
There are multiple api in k8s and many more are coming over the time.

- `kubectl api-resources` To get the list of api resources, to know which resources are in the under of which API
- `kubectl api-versions` Will tell you which version is to use to create specific resource
- `kubectl explain <name_of_an_api_resources>` explain exactly what you want to have in the YAML file to create <name_of_an_api_resources>


## Resources

- [Kubernetes Basics Modules](https://kubernetes.io/docs/tutorials/kubernetes-basics/) 
    - [x] Create a Kubernetes cluster
    - [x] Deploy an app
    - [x] Explore your app
    - [x] Expose you app locally
    - [x] Scale up your app
    - [x] Update your app
    
- [x] Install `minikube` and run all six kubernetes basics modules in locally
    
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
    - [x] [Installation Methods of Kubernetes](https://www.youtube.com/watch?v=HwVELhvL8uA&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=11)
    - [x] [Kubectl in Kubernetes](https://www.youtube.com/watch?v=5jo6gNGcs3s&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=12)
    - [x] [Play-With-K8s (PWK) in Kubernetes](https://www.youtube.com/watch?v=YAGIEyNJ3QU&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=13)
    - [x] [Minikube in Kubernetes](https://www.youtube.com/watch?v=U9BF6EpFpDc&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=14)
    - [x] [Kubernetes Components explained! Pods, Services, Secrets, ConfigMap](https://www.youtube.com/watch?v=Krpb44XR0bk&list=PLy7NrYWoggjziYQIDorlXjTvvwweTYoNC&index=2)
    - [x] [Pods and Containers - Kubernetes Networking](https://www.youtube.com/watch?v=5cNrTU6o3Fw)
    
- Full Course Videos
    - [ ] [Kubernetes Tutorial for Beginners [FULL COURSE in 4 Hours]](https://www.youtube.com/watch?v=X48VuDVv0do&t=2089s)
    
    
### Kubenetes Concepts
1. Videos
    - [x] What is Kubernetes: https://www.youtube.com/watch?v=VnvRFRk_51k
    - [x] Kubernetes Architecture:
        - [x] https://www.youtube.com/watch?v=umXEmn3cMWY
        - [x] https://www.youtube.com/watch?v=8C_SCDbUJTg
        - [x] https://www.youtube.com/watch?v=_3NUI5vasPk&t=780s
    - [x] Kubernetes Pods and Containers:
        - [x] https://www.youtube.com/watch?v=5cNrTU6o3Fw
        - [x] https://www.youtube.com/watch?v=6uvHVVNq34w&t=983s
    - [x] Kubernetes Configuration(YAML) File:
        - [x] https://www.youtube.com/watch?v=qmDzcu5uY1I&t=2s
    - [ ] Kubernetes Services:
        - [x] https://www.youtube.com/watch?v=5lzUpDtmWgM
        - [x] https://www.youtube.com/watch?v=J30_ZdaEXbw
            - [x] https://www.youtube.com/watch?v=eth7osiCryc (NodePort)
            - [x] https://www.youtube.com/watch?v=dVDElh_Kd48&t=25s(ClusterIP)
        - [ ] Ingress:
            - [ ] https://www.youtube.com/watch?v=80Ew_fsV4rM&t=671s
            - [ ] https://www.youtube.com/watch?v=VicH6KojwCI
        - [ ] EndPoint Slices:
            - [ ] https://nigelpoulton.com/blog/f/kubernetes-endpoint-slices-explained
    - [ ] Controllers:
        - [ ] https://www.youtube.com/playlist?list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ (Watch the videos on following topics from this playlist -  ReplicaSet, ReplicationController, Deployments, DaemonSet, Jobs)
        - [ ] https://www.youtube.com/watch?v=pPQKAR1pA9U (StatefulSet)
    - [x] Kubernetes Namespaces: 
        - [x] https://www.youtube.com/watch?v=VnvRFRk_51k
    - [ ] Storage
        - [ ] https://www.youtube.com/watch?v=0swOh5C3OVM (Volume, PV and PVC)
        - [ ] https://www.youtube.com/watch?v=TnfvE8o9wmg ( Volume)
        - [ ] https://www.youtube.com/watch?v=x2sMWUkasoE&t=10s (PV and PVC)
    - [ ] ConfigMap and Secret: 
        - [ ] https://www.youtube.com/watch?v=FAnQTgr04mU
    - [x] [How to Set Environment Variable in Pod's Container](https://www.youtube.com/watch?v=kVvauwwf3nM)
        
2. Topic wise Tutorials
    - [x] Overview 
        - [x] What is Kubernetes
            - [x] https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/
            - [x] https://www.youtube.com/watch?v=VnvRFRk_51k&t=1s
        - [x] Kubernetes Components
            - [x] https://kubernetes.io/docs/concepts/overview/components/
            - [x] https://www.youtube.com/watch?v=8C_SCDbUJTg
            - [x] https://www.youtube.com/watch?v=Krpb44XR0bk
            - [x] https://www.youtube.com/watch?v=umXEmn3cMWY
            - [x] https://www.youtube.com/watch?v=_3NUI5vasPk&t=780s
        - [x] The Kubernetes API
            - [x] https://kubernetes.io/docs/concepts/overview/kubernetes-api/
            - [x] https://www.youtube.com/watch?v=6CeFDkyZGKc
            - [x] https://www.youtube.com/watch?v=xQMXYO3nN30
            - [x] https://www.youtube.com/watch?v=P7QAfjdbogY
        - [x] Working with Kubernetes Objects
            - [x] Understanding Kubernetes Objects
                - [x] https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/
                - [x] https://www.youtube.com/watch?v=V96gLdlHXng
            - [x] Kubernetes Object Management
                - [x] https://kubernetes.io/docs/concepts/overview/working-with-objects/object-management/
                - [x] https://www.youtube.com/watch?v=vRcQOZLnKUk
            - [x] Object Names and IDs
                - [x] https://kubernetes.io/docs/concepts/overview/working-with-objects/names/
                - [x] https://www.youtube.com/watch?v=W5xHec3_Tts
                - [x] https://www.youtube.com/watch?v=ohSUtEfDefc
            - [x] Namespaces
                - [x] https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
                - [x] https://www.youtube.com/watch?v=K3jNo4z5Jx8
                - [x] https://www.youtube.com/watch?v=xpnZX3if9Tc
            - [x] Labels and Selectors
                - [x] https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
                - [x] https://www.youtube.com/watch?v=MHPojgrgMfQ
                - [x] https://www.youtube.com/watch?v=zsovXtOFhDE
                - [x] https://www.youtube.com/watch?v=zOXAlT5Rkfc
                - [x] https://www.youtube.com/watch?v=P2nmM0uwRfk
            - [x] Annotations
                - [x] https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/
                - [x] https://www.youtube.com/watch?v=JIaY9AXq_zM
                - [x] https://www.youtube.com/watch?v=xVcyVpxkJrk
            - [x] Field Selectors
                - [x] https://kubernetes.io/docs/concepts/overview/working-with-objects/field-selectors/
                - [x] https://www.youtube.com/watch?v=zsovXtOFhDE
            - [x] Recommended Labels
                - [x] https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/
    - [x] YAML and Configuration
        - [x] [Kubernetes YAML File Explained - Deployment and Service](https://www.youtube.com/watch?v=qmDzcu5uY1I)
        - [x] [Yaml Tutorial](https://www.youtube.com/watch?v=1uFVr15xDGg)
        - [x] [Kubernetes Deployment Tutorial - yaml explained](https://www.youtube.com/watch?v=y_vy9NVeCzo)
    - [x] Cluster Architecture
        - [x] Nodes
            - [x] https://kubernetes.io/docs/concepts/architecture/nodes/
            - [x] https://www.youtube.com/watch?v=xhwi3zIVR-8
            - [x] https://www.youtube.com/watch?v=mAaSZwPfKA0
            - [x] https://www.youtube.com/watch?v=6xJwQgDnMFE
            - [x] https://www.youtube.com/watch?v=XJufs3ZZBVY
        - [x] Control Panel-Node Communication
            - [x] https://kubernetes.io/docs/concepts/architecture/control-plane-node-communication/
            - [x] https://www.youtube.com/watch?v=uYSh1J2HxXE
            - [x] https://www.youtube.com/watch?v=VQUZF6k6g88
            - [x] https://www.youtube.com/watch?v=XNEI4AsSC7s
            - [x] https://www.youtube.com/watch?v=27v36t-3afQ
            - [x] https://www.youtube.com/watch?v=zCXiXKMqnuE
            - [x] https://www.youtube.com/watch?v=flg_A6YHlZo
        - [x] Controllers
            - [x] https://kubernetes.io/docs/concepts/architecture/controller/
            - [x] https://www.youtube.com/watch?v=55qGFj6kuOg
        - [x] Cloud Controller Manager
            - [x] https://kubernetes.io/docs/concepts/architecture/cloud-controller/
            - [x] https://www.youtube.com/watch?v=mOE1O3dQiUY
    - [x] Containers
        - [x] https://www.youtube.com/watch?v=gFozhTXOx18
        - [x] https://www.youtube.com/watch?v=5cNrTU6o3Fw
        - [x] https://www.youtube.com/watch?v=VqLcWftIaQI
        - [x] https://www.youtube.com/watch?v=wuhxSLapDe0
        - [x] Images
            - [x] https://kubernetes.io/docs/concepts/containers/images/
            - [x] https://www.youtube.com/watch?v=asIS4KIs40M
        - [x] Container Environments
            - [x] https://kubernetes.io/docs/concepts/containers/container-environment/
        - [x] Runtime Class
            - [x] https://kubernetes.io/docs/concepts/containers/runtime-class/
            - [x] https://www.youtube.com/watch?v=wSEJclx-kdk
            - [x] https://www.youtube.com/watch?v=RyXL1zOa8Bw
        - [x] Container Lifecycle Hooks
            - [x] https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/
            - [x] https://www.youtube.com/watch?v=YC0F7cOf0gE
            - [x] https://www.youtube.com/watch?v=wlYESb124xM
            - [x] https://www.youtube.com/watch?v=EGSIbjM1wh0
            - [x] https://www.youtube.com/watch?v=QLMPQPvMOH4
    - [x] Workloads
        - [x] https://kubernetes.io/docs/concepts/workloads/
        - [x] Pods
            - [x] https://kubernetes.io/docs/concepts/workloads/pods/
            - [x] https://www.youtube.com/watch?v=6uvHVVNq34w&t=983s
            - [x] https://www.youtube.com/watch?v=5cNrTU6o3Fw
            - [x] Pod Lifecycle
                - [x] https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
                - [x] https://www.youtube.com/watch?v=wlYESb124xM
            - [x] Init Containers
                - [x] https://kubernetes.io/docs/concepts/workloads/pods/init-containers/
                - [x] https://www.youtube.com/watch?v=njRtjQwuAMo
                - [x] https://www.youtube.com/watch?v=J4S_MfsCPHo
                - [x] https://en.wikipedia.org/wiki/Idempotence
            - [x] Pod Topology Spread Constraints
                - [x] https://kubernetes.io/docs/concepts/workloads/pods/pod-topology-spread-constraints/
            - [x] Pod Presets
                - [x] https://v1-18.docs.kubernetes.io/docs/concepts/workloads/pods/podpreset/
                - [x] https://www.youtube.com/watch?v=0yB7vVAmLOs
                - [x] https://www.youtube.com/watch?v=LwW6KafpTKM
            - [x] Disruptions
                - [x] https://kubernetes.io/docs/concepts/workloads/pods/disruptions/
            - [x] Ephemeral Containers
                 - [x] https://kubernetes.io/docs/concepts/workloads/pods/ephemeral-containers/
                 - [x] https://www.youtube.com/watch?v=mQ60Jm4wgFU
        - [x] Controllers/Workload Resources
            - [x] Deployments
                - [x] https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
                - [x] https://www.youtube.com/watch?v=mNK14yXIZF4
                - [x] https://www.youtube.com/watch?v=EQNO_kM96Mo
                - [x] https://www.youtube.com/watch?v=HPutXDwSWM0&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=21&t=7s
            - [x] ReplicaSet
                - [x] https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/
                - [x] https://www.youtube.com/watch?v=1As1CPuyTao
                - [x] https://www.youtube.com/watch?v=1WM-LsH6tKc&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=20
            - [x] StatefulSets
                - [x] https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
                - [x] https://www.youtube.com/watch?v=pPQKAR1pA9U
                - [x] https://www.youtube.com/watch?v=Vrxr-7rjkvM
                - [x] https://www.youtube.com/watch?v=zj6r_EEhv6s
                - [x] https://www.youtube.com/watch?v=GieXzb91I40
                - [x] https://www.youtube.com/watch?v=wR-zKkqRhz8
            - [x] DaemonSet
                - [x] https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
                - [x] https://www.youtube.com/watch?v=yYeUic8B6fM
            - [x] Jobs
                - [x] https://kubernetes.io/docs/concepts/workloads/controllers/job/
                - [x] https://www.youtube.com/watch?v=j1EnBbxSz64
                - [x] https://www.youtube.com/watch?v=O1iEBzY7-ok
            - [x] Garbage Collection
                - [x] https://kubernetes.io/docs/concepts/workloads/controllers/garbage-collection/
            - [x] TTL Controller for Finished Resources
                - [x] https://kubernetes.io/docs/concepts/workloads/controllers/ttlafterfinished/
            - [x] CronJob
                - [x] https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/
                - [x] https://www.youtube.com/watch?v=PUhqw0laR3A
                - [x] https://www.youtube.com/watch?v=OZAhYSDkhsI
            - [x] ReplicationController
                - [x] https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/
                - [x] https://www.youtube.com/watch?v=Y5ADo_tjfIs&list=PLMPZQTftRCS8Pp4wiiUruly5ODScvAwcQ&index=19
    - [ ] Services, Load Balancing, and Networking
        - [x] Service
            - [x] https://www.youtube.com/watch?v=l-h7QIQX-cA
            - [x] https://www.youtube.com/watch?v=ty9tEghAbR0
            - [x] https://kubernetes.io/docs/concepts/services-networking/service/
            - [x] https://www.youtube.com/watch?v=T4Z7visMM4E&t=22s
            - [x] https://www.youtube.com/watch?v=J30_ZdaEXbw
            - [x] https://www.youtube.com/watch?v=xCsz9IOt-fs
            - [x] https://www.youtube.com/watch?v=5lzUpDtmWgM
            - [x] https://www.youtube.com/watch?v=dVDElh_Kd48&t=25s
            - [x] https://www.youtube.com/watch?v=eth7osiCryc
            - [x] https://www.youtube.com/watch?v=NFApeJRXos4
        - [x] Service Topology
            - [x] https://kubernetes.io/docs/concepts/services-networking/service-topology/
            - [x] https://www.youtube.com/watch?v=7BZWkaqQWlc
            - [x] https://www.youtube.com/watch?v=wCeHg_FIs4g
        - [x] DNS for Services and Pods
            - [x] https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/
            - [x] https://www.youtube.com/watch?v=NFApeJRXos4
            - [x] https://www.youtube.com/watch?v=VR8ep5Wf_MA
            - [x] https://www.youtube.com/watch?v=2UancDkYJKM
            - [x] https://www.youtube.com/watch?v=osN5P2vWWvo
        - [x] Connecting Applications with Services
            - [x] https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/
            - [x] https://www.youtube.com/watch?v=OaXWwBLqugk
            - [x] https://www.youtube.com/watch?v=qYzTRcp51GE
            - [x] https://www.youtube.com/watch?v=RL-8S_K7xpc
        - [x] EndpointSlices
            - [x] https://kubernetes.io/docs/concepts/services-networking/endpoint-slices/
            - [x] https://www.youtube.com/watch?v=f3xusisgp74
            - [x] https://www.youtube.com/watch?v=fvpq4jqtuZ8
            - [x] https://nigelpoulton.com/blog/f/kubernetes-endpoint-slices-explained
        - [x] Ingress
             - [x] https://kubernetes.io/docs/concepts/services-networking/ingress/
             - [x] https://www.youtube.com/watch?v=80Ew_fsV4rM&t=671s
             - [x] https://www.youtube.com/watch?v=NPFbYpb0I7w
             - [x] https://www.youtube.com/watch?v=_BbxJGiMtL8
             - [x] https://www.youtube.com/watch?v=40VfZ_nIFWI
             - [x] https://www.youtube.com/watch?v=GhZi4DxaxxE
        - [x] Ingress Controllers
            - [x] https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/
        - [x] Network Policies
            - [x] https://kubernetes.io/docs/concepts/services-networking/network-policies/
            - [x] https://www.youtube.com/watch?v=v7_XvCW6U48
        - [x] Adding entries to Pos/etc/hosts with HostAliases
            - [x] https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/
        - [x] IPv4/IPv6 dual-stack
            - [x] https://kubernetes.io/docs/concepts/services-networking/dual-stack/
            - [x] https://www.youtube.com/watch?v=8KHSIWy_jIM
            - [x] https://www.youtube.com/watch?v=o-oMegdZcg4
    - [x] Storage
        - [x] Volumes
            - [x] https://kubernetes.io/docs/concepts/storage/volumes/#hostpath
            - [x] https://www.youtube.com/watch?v=TnfvE8o9wmg
            - [x] https://www.youtube.com/watch?v=6K6gSwJcuV4
            - [x] https://www.youtube.com/watch?v=gVuYmOlnu3A&t=547s
            - [x] https://www.youtube.com/watch?v=PdKwTlkWi14
            - [x] https://www.youtube.com/watch?v=FAnQTgr04mU
            - [x] https://www.youtube.com/watch?v=0swOh5C3OVM&t=422s
            - [x] https://www.youtube.com/watch?v=VB7vI9OT-WQ
            - [x] https://www.youtube.com/watch?v=OulmwTYTauI
        - [x] Persistent Volumes
            - [x] https://kubernetes.io/docs/concepts/storage/persistent-volumes/
            - [x] https://www.youtube.com/watch?v=x2sMWUkasoE&t=11s
        - [x] Volume Snapshots
            - [x] https://kubernetes.io/docs/concepts/storage/volume-snapshots/
        - [x] CSI Volume Cloning
            - [x] https://kubernetes.io/docs/concepts/storage/volume-pvc-datasource/
        - [x] Storage Classes
            - [x] https://kubernetes.io/docs/concepts/storage/storage-classes/
        - [x] Volume Snapshot Classes
            - [x] https://kubernetes.io/docs/concepts/storage/volume-snapshot-classes/
        - [x] Dynamic Volume Provisioning
            - [x] https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/
            - [x] https://www.youtube.com/watch?v=XSNuDl3wHuc&t=75s
            - [x] https://www.youtube.com/watch?v=LQjQvN_HXvY&t=80s
        - [x] Storage Capacity
            - [x] https://kubernetes.io/docs/concepts/storage/storage-capacity/
        - [x] Ephemeral Volumes
            - [x] https://kubernetes.io/docs/concepts/storage/ephemeral-volumes/
        - [x] Node-specific Volume Limits
            - [x] https://kubernetes.io/docs/concepts/storage/storage-limits/
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
    - [ ] Others
        - [ ] [Yaml Techniques](https://github.com/helm/helm/blob/v2.16.5/docs/chart_template_guide/yaml_techniques.md)
        - [x] https://www.youtube.com/watch?v=Pv5yKnOGSSc&list=PLoiT1Gv2KR1gE-WK7dIQ5bsJ_RAVZnzP8&index=7
        - [x] [Kubernetes Networking Related Some Excellent Articles](https://cloudnativelabs.github.io/)
        - [x] [Setting Up an Ingress Controller](https://kind.sigs.k8s.io/docs/user/ingress/)