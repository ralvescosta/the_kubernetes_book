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