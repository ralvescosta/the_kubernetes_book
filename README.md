# The Kubernetes Book 2022 Edition
by Nigel Poulton & Pushkar Joglekar

## 2: Kubernetes principles of operation

### Control Plane and Worker Nodes

  - **Control Plane**: 

    - Nodes runs a collection of system services that make up the control plane of the cluster. Some of theses services are:

      - **API Server**:

        - Control plane front-end, all instructions and communication pass through it. By default, it exposes a RESTFull endpoints on port 443.

      - **Cluster Store**:

        - etcd

      - **Controller manager and controllers**: 

        - Implements all the background controllers that monitor cluster components and respond to events.

        - Controller of controllers

        - Deployment controller

        - StatefulSet controller

        - ReplicaSet controller

      - **Scheduler**:
        
        - Responsible for piking the nodes to run tasks, to do that the scheduler run a complex algorithm to get a "ranking" about all of the nodes available. To do this "rank" the scheduler check if the node is capable to run the task.

      - **Cloud Controller Manager**:

        - When our K8s is running at a public could, the control planning will contain this service helping the integration with the cloud services.

  - **Worker Nodes**:

    Where user applications run. They do three things

      1. Watch the API server for new work assignments
      2. Execute work assignments
      3. Report back to the control plane

    The major components of a worker node are:

      - **Kubelet**:

        - The main K8S agent and runs every worker node.
        - Execute the control plane tasks and report the results

      - **Container runtime**:

      - **Kube-proxy**:

        - Responsible for local cluster networking.

  - **Kubernetes DNS**:

    - Internal DNS service based on CoreDNS
  
  - **Packaging apps for Kubernetes**:

    1. Packaged as a container

    2. Wrapped in a Pod

    3. Deployed via a declarative manifest file

  - **The declarative model and desired state**:

    - Declare de desired state

    - Post it to the API

    - K8S implements the desired state on the cluster

    - A controller makes sure the observer state of the application does not vary from the desired state.

  - **Pods**:

    - In the K8S the atomic unit of scheduling is the Pod.

    - Inside the Pod we could have one container or some containers. It's more normal to had one container inside a Pod

  - **Pod anatomy**:

    - Keeping it high level, Pods ring-fence an area of the host OS, build a network stack, create a bunch of kernel namespaces, and run one or mode containers. If you're running multiple containers in a pod, they all share the same Pod environment.

    - If two containers in the same Pod need to talk to each other they can use Pod's localhost interface.
    
  - **Pod lifecycle**:

    - Pods are mortal. They're created, they live and they die. If the die unexpectedly you do not bring then back to life. Instead K8S stars a new one in its place.

  - **Pod immutability**:

    - Pods are also immutable - this means you do not change them once they're running. For example, once a Pod is running, you never log on to it and change or update irs configuration. If you need to change or update it, you replace it with a new one. Whenever we talk about updating Pods, we really mean delete the old one and replace it with a new one.

  - **Deployments**:

    - Deployment is a higher-level K8S object that wraps around a Pod and adds features such as self-healing, scaling, zero-downtime rollout and versioned rollbacks.
  
  - **Service objects and stable networking**:

    - Because of the Pod volatility we need to use a tool to help to handle the network traffic. Every time a new Pod is created or a old Pod is deleted we need to now which Pod we can communicate and K8S use the Service to do that. the Service provides a reliable name and IP it's also load-balancing the request to the "Same Pods"

## 3: Getting Kubernetes

  - **Kubectl**:

    - The kubectl configuration file is called config and lives in a hidden directory called kub in your home directory ($HOME/.kube/config) with definitions for:

      - Clusters
        - A single kubectl can manage multiple cluster.
      - Users (credentials)
        - You can have a lot of users configured.
      - Contexts
        - Group together clusters and users under a friendly name. Ex: you might have a context called *ops-prod* that combines the *ops* user credentials with the *prod* cluster definition. If you use kubectl with this context, you'll be sending a commands to the API server of the *prod* cluster as the *ops* user. You can view your configs using this command: *kubectl config view*
  
## 4: Working with Pods

  - **Pod theory**:

    - The atomic unit of scheduling in K8S is the Pod.

    - The process of building and running an app on Kubernetes is roughly as follows:

      1. Write you app/code
      2. Package it as container image
      3. Wrap the container image in a Pod
      4. Run it on Kubernetes

  - **Pods augment containers**:

    - Labels and annotations
    - Restart policies
    - Proves (startup probes, readiness probes, liveness probes and potentially more)
    - Affinity and anti-affinity rules
    - Termination control
    - Security policies
    - Resource requests and limits
  
    - The command bellow list all possible Pod attributes:

      ```bash
        kubectl explain pods

        kubectl explain pods --recursive
      ```

    - The command bellow show the specific attributes documentation:

      ```bash
        kubectl explain pod.spec.restartPolicy
      ```

    - Some Pods features:

      - *Label*: Let you group Pods and associate then with other objects in powerful way.
      - *Annotations*: Let you add experimental features and integrations with 3rd-party tools and services.
      - *Probes*: Let you test the health and status of Pods and the apps they run. This enable advanced scheduling, updates and more.
      - *Affinity and Anti-Affinity*: Rules give you control over where in cluster Pods are allowed to run.
      - *Termination control*: Lets you to gracefully terminate Pods and the applications they run.
      - *Security polices*: Let you enforce security features.
      - *Resource requests and limits*: Let you specify minimum and maximum values for things like CPU, memory and disk IO.

    - How to declare a Pod Object:

      ```yml
        apiVersion: v1
        kind: Pod
        metadata:
          name: hello-pod
          labels:
            zone: prod
            version: v1
        spec:
          container:
            - name: hello-ctr
              image: nigelpoulton/k8sbook:1.0
              ports:
              - containerPort: 8080
      ```

  - **Pods enable resource sharing**:
    
    - Pods provide a shared execution environment for one or more containers:

      - shared filesystem
      - shared network stack
      - shared memory
      - shared volumes

  - **Static Pods vs controllers**:

    - There are two ways to deploy pods:

      - Directly via a Pod manifest (static pods)
      - Indirectly via controller
    
    - Pods deploy directly have no super-powers such as self-healing, scaling or rolling updates because the're only monitored and managed by the worker node's kubelet process, there is no control plane process watching and capable of stating a new one on a different node.
    
    - Pods deployed via controllers have all the benefits of being monitored and managed by a highly-available controller running on the control plane.
  
  - **Deploying Pods**:

    - The process of deploying a Pod to Kubernetes is as follows.

      1. Define it in a YAML manifest file
      2. Post the yaml to the API server
      3. The API server authenticates and authorizes the request
      4. The configuration (YAML) is validated
      5. The scheduler deploy the Pod to a healthy worker node with enough available resources
      6. The local kubelet monitors it

  - **Pods and shared networking**:

    - Each pod creates its own network namespace. This means aa Pod has its own IP address, a single range of TCP and UDP pots and a single routing table.

  - **The pod network**:

    - On the topic of networking, every pod gets irs own unique IP address that's fully routable on an internal K8S network called the pod network.
  
  - **Atomic deployment of a Pods**:

    - This means it's all-or-nothing deployment, succeeds or fails.

  - **Short-lived and lob-lived Pods**:

    - If any containers in long-lived Pod fail, the local kubelet may attempt to restart them. *Always* is the default restart policy and appropriate for most long-lived Pods.
    - Appropriate container restar polices for short-lived Pods will usually be *Never* or *OnFailure*.
    - *Deployment*, *StatefulSets* and *DaemonSets* are examples of controllers designed for long-lived Pods.
    - *Jobs* and *Cronjobs* are examples of controllers designed for short-lived Pods.
  
  - **Pod immutability**:

    - Pods are immutable objects. This means you can't modify them after they're deployed.
    - Always replace a Pod for a new one.
  
  - **Pods and scaling**:

    - *Horizontal Scaling*, when we need to scale we add or remove pods.

  - **Multi-container Pods**:

    - At a very high-level, every container should have a single clearly defined responsibility.

    - K8S offers several well-defined multi-container Pod patterns:

      - Sidecar pattern
      - Adapter pattern
      - Ambassador pattern
      - Init pattern

  - **Sidecar multi-container Pods**:

    - The most popular multi-container patter.
    - It has a main application container and a *sidecar* container.
    - Used in the service mesh.

  - **Adapter multi-container Pods**:
   
    - Help container to take non-standardized output from the main container and rejigs it into a format required by an external system.
    - Exemplo: Send Nginx logs to Prometheus

  - **Ambassador multi-container Pods**:

    - Help containers brokers connectivity to an external system.

  - **Init multi-container Pods**:

    - Running a special *init container* that's guaranteed to start and complete before your main app container. It's guaranteed to only run once.