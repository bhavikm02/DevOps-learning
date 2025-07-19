# EKS Interview Questions and Answers

Below is a list of 50 basic to intermediate questions about Amazon Elastic Kubernetes Service (EKS). Simple diagrams are provided where possible to help visualize concepts.

## Table of Contents

1. [What is Amazon EKS?](#q1)
2. [How does EKS manage the Kubernetes control plane?](#q2)
3. [What IAM roles are associated with an EKS cluster?](#q3)
4. [How do you install `kubectl` for EKS?](#q4)
5. [What is an EKS worker node?](#q5)
6. [How does the EKS VPC architecture look?](#q6)
7. [Can you run Windows containers on EKS?](#q7)
8. [How do you update an EKS cluster?](#q8)
9. [What is the difference between Fargate and managed node groups?](#q9)
10. [How does EKS integrate with IAM for authentication?](#q10)
11. [What are security groups in EKS?](#q11)
12. [How do you enable logging for an EKS cluster?](#q12)
13. [What are Kubernetes add-ons in EKS?](#q13)
14. [How do you monitor EKS clusters?](#q14)
15. [What is the Cluster Autoscaler?](#q15)
16. [How does EKS handle service discovery?](#q16)
17. [What is the AWS Load Balancer Controller?](#q17)
18. [What is the difference between Kubernetes Services and Ingress?](#q18)
19. [How do you manage secrets in EKS?](#q19)
20. [How does EKS support multi-AZ deployments?](#q20)
21. [What is an EKS managed policy?](#q21)
22. [What is the purpose of `kubeconfig`?](#q22)
23. [How do you access the Kubernetes dashboard in EKS?](#q23)
24. [What is the role of CloudFormation in EKS?](#q24)
25. [How can you run stateful workloads in EKS?](#q25)
26. [How do you configure network policies?](#q26)
27. [What is the purpose of AWS App Mesh with EKS?](#q27)
28. [How do you integrate CI/CD with EKS?](#q28)
29. [What is the difference between pods and deployments?](#q29)
30. [How do you upgrade worker nodes?](#q30)
31. [How can you enable encryption of Kubernetes secrets at rest?](#q31)
32. [What is a service account in EKS?](#q32)
33. [How do you configure horizontal pod autoscaling?](#q33)
34. [What does the `eksctl` tool do?](#q34)
35. [How do you set up Amazon ECR with EKS?](#q35)
36. [How does EKS handle persistent storage?](#q36)
37. [What are EKS add-ons?](#q37)
38. [What is the difference between EKS and self-managed Kubernetes?](#q38)
39. [How do you configure custom domains with Ingress?](#q39)
40. [How do you manage cluster networking with CNI plugins?](#q40)
41. [What is the AWS Node Termination Handler?](#q41)
42. [How do you use AWS PrivateLink with EKS?](#q42)
43. [What is the difference between Spot Instances and On-Demand in EKS?](#q43)
44. [How do you enforce pod security policies?](#q44)
45. [How do you secure API server access?](#q45)
46. [What is the role of kube-proxy in EKS?](#q46)
47. [How do you back up and restore EKS clusters?](#q47)
48. [What is the purpose of the Amazon VPC CNI plugin?](#q48)
49. [How do you leverage Helm in EKS?](#q49)
50. [How can you integrate EKS with other AWS services?](#q50)

---

---

### <a name="q1"></a>1. What is Amazon EKS?
Amazon Elastic Kubernetes Service (EKS) is a managed Kubernetes service that simplifies running Kubernetes on AWS. EKS automatically manages the Kubernetes control plane so you can focus on your applications.

```
+--------------+       +---------------------+
|   Users      +-----> |   EKS Control Plane |
+--------------+       +---------------------+
```

### <a name="q2"></a>2. How does EKS manage the Kubernetes control plane?
EKS runs and scales the Kubernetes API servers and etcd across multiple Availability Zones. AWS handles the availability and scalability of the control plane.

```
API Server -> etcd (multi-AZ managed by AWS)
```

### <a name="q3"></a>3. What IAM roles are associated with an EKS cluster?
Typically you have an IAM role for the EKS cluster to interact with other AWS services and roles for worker nodes. You also use IAM roles for service accounts to give specific permissions to pods.

```
[EKS Cluster Role] <----> [Node IAM Role]
```

### <a name="q4"></a>4. How do you install `kubectl` for EKS?
Use the AWS CLI to download and install a version compatible with your cluster. For example:
`curl -o kubectl https://s3.us-west-2.amazonaws.com/amazon-eks/1.27.4/2023-11-22/bin/linux/amd64/kubectl`

```
Workstation --> kubectl --> EKS Cluster
```

### <a name="q5"></a>5. What is an EKS worker node?
Worker nodes run your Kubernetes workloads. They can be EC2 instances (self-managed or managed node groups) or AWS Fargate tasks.

```
[EKS Control Plane] --> [EC2 Worker Node]
```

### <a name="q6"></a>6. How does the EKS VPC architecture look?
Each EKS cluster runs in a VPC with subnets for control plane endpoints and worker nodes. Typically you use private subnets for nodes and public subnets for load balancers.

```
VPC
|--Public Subnet
|--Private Subnet
```

### <a name="q7"></a>7. Can you run Windows containers on EKS?
Yes. EKS supports Windows worker nodes and Windows container workloads starting with Kubernetes 1.14 and later.

```
Linux Node + Windows Node in same EKS Cluster
```

### <a name="q8"></a>8. How do you update an EKS cluster?
Use either the AWS Management Console, AWS CLI, or `eksctl` to update the control plane version. Then update worker nodes to match.

```
Update Control Plane -> Update Node Groups
```

### <a name="q9"></a>9. What is the difference between Fargate and managed node groups?
Fargate launches pods in serverless compute; managed node groups run EC2 instances managed by AWS. Fargate is simpler but with less control.

```
[Fargate Pod] vs [Managed EC2 Node]
```

### <a name="q10"></a>10. How does EKS integrate with IAM for authentication?
IAM users and roles are mapped to Kubernetes RBAC via the `aws-auth` ConfigMap to control cluster access.

```
IAM User --> aws-auth ConfigMap --> Kubernetes RBAC
```

### <a name="q11"></a>11. What are security groups in EKS?
Security groups act as virtual firewalls for your cluster nodes and control inbound and outbound traffic.

```
[Security Group] -> [Worker Node]
```

### <a name="q12"></a>12. How do you enable logging for an EKS cluster?
Use the AWS CLI or console to enable control plane logging to CloudWatch for components like API server, scheduler, and controller manager.

```
EKS Control Plane --> CloudWatch Logs
```

### <a name="q13"></a>13. What are Kubernetes add-ons in EKS?
Add-ons are curated versions of common operational software such as the Amazon VPC CNI, kube-proxy, or CoreDNS that AWS maintains for EKS clusters.

```
[Add-on] -> [Cluster]
```

### <a name="q14"></a>14. How do you monitor EKS clusters?
You can use Amazon CloudWatch Container Insights, Prometheus, or other observability tools to gather metrics and logs.

```
Cluster Metrics --> CloudWatch / Prometheus
```

### <a name="q15"></a>15. What is the Cluster Autoscaler?
The Cluster Autoscaler automatically adjusts the number of nodes in your node groups based on pod demand.

```
Pods Pending --> Cluster Autoscaler --> Scale Node Group
```

### <a name="q16"></a>16. How does EKS handle service discovery?
EKS uses CoreDNS for service discovery within the cluster, resolving service names to cluster IP addresses.

```
Pod DNS Query -> CoreDNS -> Service IP
```

### <a name="q17"></a>17. What is the AWS Load Balancer Controller?
It automates the creation of Application Load Balancers or Network Load Balancers for Kubernetes services and ingress resources.

```
Ingress -> AWS Load Balancer Controller -> ALB/NLB
```

### <a name="q18"></a>18. What is the difference between Kubernetes Services and Ingress?
A Service exposes a set of pods, often through a load balancer or cluster IP. Ingress manages external access to services via HTTP(S) routes.

```
Service <--> Pod Set
Ingress -> HTTP rules -> Service
```

### <a name="q19"></a>19. How do you manage secrets in EKS?
You can store secrets in Kubernetes secrets, use AWS Secrets Manager, or integrate with AWS Systems Manager Parameter Store.

```
Secret -> Pod using Volume or Env Var
```

### <a name="q20"></a>20. How does EKS support multi-AZ deployments?
EKS automatically deploys the control plane across multiple Availability Zones and you can spread worker nodes across AZs for resilience.

```
AZ1 + AZ2 + AZ3 -> Control Plane & Nodes
```

### <a name="q21"></a>21. What is an EKS managed policy?
AWS provides IAM managed policies specifically for EKS, such as `AmazonEKSClusterPolicy` and `AmazonEKSWorkerNodePolicy`, to simplify permissions.

```
IAM Managed Policy -> Role -> EKS
```

### <a name="q22"></a>22. What is the purpose of `kubeconfig`?
`kubeconfig` defines cluster connection info for `kubectl`, including server address and authentication details.

```
.kube/config -> kubectl -> Cluster
```

### <a name="q23"></a>23. How do you access the Kubernetes dashboard in EKS?
Deploy the Kubernetes dashboard manifest, then use `kubectl proxy` or an Ingress to access it securely.

```
User -> kubectl proxy -> Dashboard
```

### <a name="q24"></a>24. What is the role of CloudFormation in EKS?
CloudFormation can provision EKS clusters, node groups, and related infrastructure as code.

```
CloudFormation Template -> EKS Cluster + Nodes
```

### <a name="q25"></a>25. How can you run stateful workloads in EKS?
Use StatefulSets with persistent storage backed by EBS, EFS, or other CSI drivers.

```
StatefulSet -> PVC -> EBS Volume
```

### <a name="q26"></a>26. How do you configure network policies?
Install a CNI that supports network policies, then define ingress and egress rules with Kubernetes NetworkPolicy objects.

```
NetworkPolicy -> Calico/Other -> Pod Traffic
```

### <a name="q27"></a>27. What is the purpose of AWS App Mesh with EKS?
App Mesh provides a service mesh for microservices, offering traffic routing, observability, and security features.

```
Pod -> Envoy Sidecar -> App Mesh
```

### <a name="q28"></a>28. How do you integrate CI/CD with EKS?
Use pipelines (for example, AWS CodePipeline or GitHub Actions) to build images, push to ECR, and deploy to EKS using `kubectl` or Helm.

```
Code -> Build -> ECR -> Deploy to EKS
```

### <a name="q29"></a>29. What is the difference between pods and deployments?
A pod is the smallest deployable unit in Kubernetes. A deployment maintains a desired number of pod replicas and handles updates.

```
Deployment -> ReplicaSet -> Pods
```

### <a name="q30"></a>30. How do you upgrade worker nodes?
Create a new node group with the desired version or update the existing group and then drain and terminate old nodes.

```
New Node Group <- Migrate Pods -> Remove Old Nodes
```

### <a name="q31"></a>31. How can you enable encryption of Kubernetes secrets at rest?
Enable envelope encryption using AWS KMS for secrets stored in etcd by configuring encryption providers.

```
KMS Key -> Etcd -> Encrypted Secrets
```

### <a name="q32"></a>32. What is a service account in EKS?
A service account provides an identity for processes running in a pod so that you can associate IAM roles with those pods.

```
ServiceAccount -> IAM Role -> Pod
```

### <a name="q33"></a>33. How do you configure horizontal pod autoscaling?
Deploy the Metrics Server, then create an HPA object that scales pod replicas based on CPU or other metrics.

```
Metrics -> HPA -> Adjust Pod Count
```

### <a name="q34"></a>34. What does the `eksctl` tool do?
`eksctl` is a CLI that simplifies creating and managing EKS clusters and node groups using simple commands and configuration files.

```
eksctl create cluster -> EKS + Node Group
```

### <a name="q35"></a>35. How do you set up Amazon ECR with EKS?
Build and push container images to Amazon ECR, then reference the ECR repository in your pod specifications.

```
Docker Build -> Push to ECR -> Pod Pulls Image
```

### <a name="q36"></a>36. How does EKS handle persistent storage?
EKS supports Kubernetes storage classes for Amazon EBS, EFS, and FSx via CSI drivers.

```
Pod -> PVC -> Storage Class -> EBS/EFS
```

### <a name="q37"></a>37. What are EKS add-ons?
Add-ons are AWS-managed deployments of common cluster components that you can install directly through the EKS console or CLI.

```
EKS Add-on -> Managed Upgrade
```

### <a name="q38"></a>38. What is the difference between EKS and self-managed Kubernetes?
EKS offloads control plane management to AWS, provides integrated IAM and VPC networking, and simplifies upgrades compared to self-managed clusters on EC2.

```
EKS (managed) vs Self-Managed Control Plane
```

### <a name="q39"></a>39. How do you configure custom domains with Ingress?
Use an Ingress controller with annotations for your DNS provider or ALB, then create a DNS record pointing to the load balancer.

```
DNS -> ALB -> Ingress -> Service
```

### <a name="q40"></a>40. How do you manage cluster networking with CNI plugins?
EKS uses the Amazon VPC CNI plugin by default, but you can also install alternative CNIs like Calico for network policies and advanced features.

```
Pods -> VPC CNI -> ENIs in VPC
```

### <a name="q41"></a>41. What is the AWS Node Termination Handler?
It gracefully handles EC2 spot interruption or maintenance events by draining pods before the instance is terminated.

```
Termination Notice -> Drain Pods -> Terminate Node
```

### <a name="q42"></a>42. How do you use AWS PrivateLink with EKS?
PrivateLink allows private access to EKS API endpoints from your VPC without traversing the public internet.

```
VPC Endpoint -> PrivateLink -> EKS API
```

### <a name="q43"></a>43. What is the difference between Spot Instances and On-Demand in EKS?
Spot Instances are spare EC2 capacity offered at a discount but can be interrupted. On-Demand provides predictable availability at a fixed price.

```
Spot Node (cheaper) vs On-Demand Node
```

### <a name="q44"></a>44. How do you enforce pod security policies?
Pod security policies (deprecated in newer Kubernetes versions) or the new Pod Security Admission can restrict container privileges and capabilities.

```
PodSecurity -> Admission Controller -> Pod
```

### <a name="q45"></a>45. How do you secure API server access?
Use IAM authentication, restrict security groups, and apply network ACLs or VPN/PrivateLink to limit access to the API endpoint.

```
User -> IAM -> EKS API (secured)
```

### <a name="q46"></a>46. What is the role of kube-proxy in EKS?
Kube-proxy maintains network rules on worker nodes to route traffic to the correct pods across the cluster.

```
Service IP -> kube-proxy -> Pod IP
```

### <a name="q47"></a>47. How do you back up and restore EKS clusters?
Use tools like `velero` or AWS Backup with EBS snapshots and EKS-specific integrations to back up cluster state and persistent volumes.

```
Cluster Data -> Velero -> Backup Storage
```

### <a name="q48"></a>48. What is the purpose of the Amazon VPC CNI plugin?
It allows each pod to receive an IP address from the VPC, enabling native networking and integration with VPC features.

```
Pod -> ENI -> VPC Network
```

### <a name="q49"></a>49. How do you leverage Helm in EKS?
Helm helps package Kubernetes applications as charts. You can install them in your EKS cluster to manage complex deployments.

```
helm install chart -> Resources in EKS
```

### <a name="q50"></a>50. How can you integrate EKS with other AWS services?
EKS integrates with many AWS services such as IAM, CloudWatch, ALB/NLB, ECR, KMS, and more to provide a fully managed ecosystem.

```
EKS -> IAM, CloudWatch, ALB, ECR, ...
```

