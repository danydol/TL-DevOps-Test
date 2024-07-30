Hi, 

Welcome to our Git Repository for your DevOps skills assessment.

To begin:
1. Fork this repository.
2. Complete the tasks as outlined in the provided instructions.
3. Add your work and documentation to your forked repository.
4. Send us a link to your repository or zip and send us the files

Good luck!

# *Part 1: General Questions*
1. How would you implement devops for Mןicrosoft SQL Server Database - maintain history, releases, version control
Answer:
Need to store my database schema, scripts, and configuration files in Version contorl,
such a GitHub.

///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
To manage all flow, we need to decide which strategy to implement. For example, if I use the Git Flow Branch Strategy, the main idea of this strategy is to use multiple branches for each use case. For instance, store your database schema, scripts, and configuration files first in the main branch. This branch saves only release versions. Use the dev branch for main development, and create feature branches from dev. When developers finish a new version, it should be saved in the releases branch. There are some cases where the releases are stored in the main branch.
To fix bugs in earlier versions, the developer should use the release/main branch and create a fix branch from that branch, then merge it into the dev branch with a new tag. Finally, merge it again into the release/main branch.
All pull requests should be checked by code analysis or a team lead before merging. Additionally, all CI processes should be automated using tools like Azure DevOps, GitHub Actions, etc. In this CI process, stages need to be implemented that check code and vulnerabilities. For each commit or pull request, the CI process will start, which includes building, code analysis, and vulnerability scanning. Another stage will involve testing, such as unit, stress, and performance testing. The final stage will upload artifacts to an artifact repository system like JFrog, Nexus, etc.
To implement new code on production, staging, or development environments, you need to run the CD process using the same tool or apply GitOps methodology.
/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

2. What is role of promote stage in ci-cd process.

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
The promote stage is a very important role in moving between environments. For example, if my artifact has been tested in the development environment and has passed all tests and the CI/CD process is completed successfully, the artifact can be moved to new environments such as staging and production.

For instance, in JFrog Artifactory, which also supports the CI process, there is a promote role that saves time during the promotion process. It streamlines the build process by not strat all building process again , and after the CI process is successfully completed, the artifact is moved to another repository or environment, and so on.

///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////


3. Suppose there is an app which stores state in its memory - you want to enable working with multiple instance of this app at the same time with high availability - how will you achieve that?

///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

To work with multiple instances app that stores state in its memory and achive HA such as redis cache cluster, we should create cluster of nodes with Primary and Replicas and , its create shards (partitions) data across multiple nodes and Each node is responsible for a subset of the keyspace or Hash Slot, Each primary node manages a subset of the data, and its corresponding replica nodes replicate that data
Master nodes handle write requests, while slaves replicate the data for read scalability
For HA issues- If a master fails, a reachable replica can be promoted to a new primary 
Replicas migration ensures that all primary servers have at least one replica

This is an example on redis cache cluster, Elastic search is working very similar to this, elastic use also memry in cache and also store data on disk, Its working also with sharding acrros multiple nodes and primary server that hold all metadata

///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

4. How do you perform a Kubernetes upgrade with zero downtime?

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Before we starting the upgrdae we need to valdiate that etcd is backuped, also chack all api app version to validate id the new k8s version is supported on app api versions
If i am using onprem kubeadm cluster in need to run kubeadm upgrade plan, than check all control plan and worker nodes versions    
I will start with my first control plan i will drain  this node so pods can not be hosted on this node and all other pods will evicted and need to run the upgrade command, if the CNI is deamonset no need to upgrdade it, if its not need to upgrdae it manually, after SUCCESS upgrade need to run uncordon- using kubeadm upgrade also automatically renews the certificates
I will do it for each control plan 
After i will finish with the control plan i will do this on each worker

In the cloud, for example, on GCP, the upgrade is done automatically without downtime, as is the case with other cloud providers

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

# *Part 2: Kubernetes hands on*

1. **Cluster Setup:**
   - Provision a Kubernetes cluster using a tool like `kubeadm`, `kops`, or `kind`.
  
   - **Done with Minikube**

2. **Nginx Deployment:**
   - Deploy nginx using helm this chart [nginx Helm](https://github.com/kubernetes/ingress-nginx/blob/main/charts/ingress-nginx). Done
   - When you will deploy the chart as DaemonSet, when not. What are the disatvantage/advantage of a DaemonSet.
  
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

I deploy the chart as DaemonSet when i want that each node will be automatically schedule the  pods on all nodes
I wont do it when dont wnat this be automatically schedule and when also if i have a resource limitation
  Advantages:
- DaemonSets are ideal for running node-specific tasks such as logging, monitoring, and system management, ensuring that each node runs the required service.
- By running the same pod on every node, DaemonSets help maintain a consistent environment across the cluster.
- DaemonSets simplify the deployment and management of critical services across all nodes, reducing the complexity of managing individual pod deployments on each node.
-  When new nodes are added to the cluster, DaemonSets automatically schedule the necessary pods on these nodes, ensuring that the service scales with the cluster.
Disadvantages:
-  In very large clusters, having a DaemonSet pod on every node can lead to management and scaling challenges, especially if the DaemonSet pods consume significant resources, also if we need to 
   excluded some nodes from deamonset we need to mange toleration and taints, node selctors ffinity/anti-affinity rules etc ...
-  Running a pod on every node can lead to higher resource consumption, which might not be necessary for all applications, potentially leading to resource wastage

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

What will you choose if you need to use aws load balancer?


/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Its depened what the peorpose Thete is 4 major types of LB:
- NLB - Layer 4	
Supports TCP/UDP,TLS protocols, high performance 

- ALB- Layer 7	
Supports on Http/Https traffic supports advanced routing features such as host-based or path-based routing like mapping on api gateway
- ELB - Layer 4/7
  Supports both HTTP/HTTPS and TCP protocols, used for legacy applications that don't require advanced features

GLB - Layer 3 Gateway - Layer 4 Load Balancing
- Useful for use cases requiring integration with third-party appliances like firewalls 


To pick one of those load balancers, it depends on the application type and the cost of the service. For HTTP/HTTPS traffic, which operates at Layer 7, I will pick the ALB if I need advanced application configuration, such as path-based routing (e.g., www.example.com/test). For Layer 4 and TCP/TLS traffic, I will choose the NLB. Other load balancers are less relevant for most scenarios.


/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
ל

  

   - How will you deploy the aws load balancer to work with eks

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Install ingress controller on EKS cluster with anotation of NLB st certification at ACM (aws certification manager)
Edit the service/loadbalancer-aws-elb.yaml with service.beta.kubernetes.io/aws-load-balancer-type: nlb defenition as annotation
add anotation of the ACM 
When i will create ingress resource i will add the FQDN  and TLS to ingress resource and it will update the NLB target (action defualt) and TLS  listner with 443 port on NLB also will be update
In Route53 i will create A record with route alias to NLB


///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////


3.  **App Deployment:**
    - Deploy Jenkins (can be other app you choose) with helm [Jenkins Helm](https://github.com/jenkinsci/helm-charts/blob/main/charts/jenkins). 
          
3. **Scaling:**
   - Implement horizontal and vertical pod autoscaling for nginx using helm values (in the values file)
   - What is keda for auto scaling, why to use Keda?
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

  KEDA is a Kubernetes-based Event Driven Autoscaler. With KEDA, you can drive the scaling of any container in Kubernetes based on the number of events needing to be processed 
  The Autoscale perform only on CPU/Memory by event driven such as prometehus, kafka, RabitMQ, SQS etc ...
  KEDA supports a wide range of event sources, making it suitable for both cloud and on-premises deployments
  Modern applications are increasingly adopting event-driven architectures, where services communicate asynchronously through events 
  Support open telemetry 
  //////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
  

     
4. **Networking:**
   - How would you restrict network between apps within the cluster? Write an example.
   - Expose the Jenkins applications using Ingress.

5. **Storage:**
   - Configure Persistent Volumes (PVs) and Persistent Volume Claims (PVCs) for Jenkins

6. **Security:**
   - Implement an example Role-Based Access Control (RBAC) on one of the namespace for example restrict a user from some namespace, 
   - What is the purpose of the of the service account - how can it be useful in scenarios over aws.

