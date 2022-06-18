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