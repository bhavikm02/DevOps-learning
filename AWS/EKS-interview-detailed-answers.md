# EKS Interview Detailed Answers

This document contains detailed answers to each of the 50 EKS interview questions. Each answer includes practical examples from common industry scenarios.

## 1. What is Amazon EKS?
Amazon Elastic Kubernetes Service (EKS) is a managed service that runs upstream Kubernetes on AWS without needing you to manage the control plane components. In production environments I've worked with, EKS removes the operational burden of maintaining etcd and the API server, allowing the team to focus on application deployment and scaling.

## 2. How does EKS manage the Kubernetes control plane?
EKS provisions and manages the Kubernetes control plane across multiple AWS Availability Zones, ensuring high availability. For example, at one company we used EKS because AWS handled scaling the control plane automatically, which meant we didn't need to worry about sizing the master nodes during traffic spikes.

## 3. What IAM roles are associated with an EKS cluster?
You typically create an IAM role for the cluster itself so it can interact with other AWS services like VPC or CloudWatch. Worker nodes also have roles to pull container images and send logs. In a previous project, we added IAM roles for service accounts so specific pods could access S3 without exposing credentials.

## 4. How do you install `kubectl` for EKS?
You download the appropriate version from the AWS S3 bucket and make it executable. In practice, we automated this in our CI pipeline so developers always used the same version of `kubectl` that matched the cluster.

## 5. What is an EKS worker node?
Worker nodes are EC2 instances or Fargate tasks that run your containers. I've managed deployments with managed node groups where AWS handled the underlying Auto Scaling group, simplifying node lifecycle management.

## 6. How does the EKS VPC architecture look?
EKS clusters reside in your own VPC. Typically we create public subnets for load balancers and private subnets for worker nodes. In production, we enabled VPC flow logs to monitor network traffic between services.

## 7. Can you run Windows containers on EKS?
Yes. I've deployed mixed clusters with Linux and Windows nodes to support legacy .NET applications alongside modern microservices. EKS makes it straightforward to schedule Windows pods using taints and tolerations.

## 8. How do you update an EKS cluster?
We use the AWS CLI or `eksctl` to upgrade the control plane version, then upgrade each node group. In one engagement, we staged upgrades in a test environment first to validate our workloads before updating production.

## 9. What is the difference between Fargate and managed node groups?
Fargate is serverless, so AWS manages the underlying compute and you only worry about pod specs. Managed node groups provide EC2 instances that you can customize. In our organization, Fargate was great for sporadic workloads, while node groups gave us more control for high-throughput services.

## 10. How does EKS integrate with IAM for authentication?
EKS uses the `aws-auth` ConfigMap to map IAM roles or users to Kubernetes RBAC. At a former employer, we mapped CI pipelines to dedicated IAM roles so each deployment had least-privilege cluster access.

## 11. What are security groups in EKS?
Security groups are virtual firewalls for EC2 instances. We attached different security groups to worker nodes and load balancers to restrict inbound traffic only from known sources, improving our overall security posture.

## 12. How do you enable logging for an EKS cluster?
You enable control plane logging through the AWS console or CLI, which sends logs to CloudWatch. We often enabled API server and scheduler logs to troubleshoot issues during cluster upgrades.

## 13. What are Kubernetes add-ons in EKS?
Add-ons are AWS-managed versions of tools like the Amazon VPC CNI, CoreDNS, or kube-proxy. Using add-ons, we could consistently upgrade these components across clusters without manual manifests.

## 14. How do you monitor EKS clusters?
We used CloudWatch Container Insights and Prometheus to track CPU, memory, and custom metrics. Alerting on these metrics helped us catch issues such as runaway resource usage before they affected customers.

## 15. What is the Cluster Autoscaler?
The Cluster Autoscaler adjusts the size of a node group based on unscheduled pods. In production, this kept our costs under control by adding nodes only when workloads demanded more resources.

## 16. How does EKS handle service discovery?
EKS relies on CoreDNS to resolve service names to cluster IPs. We used this for internal microservices communication, along with Route 53 private hosted zones for external dependencies.

## 17. What is the AWS Load Balancer Controller?
This controller automatically provisions Application Load Balancers or Network Load Balancers for Kubernetes Ingresses and Services. In one project, it simplified exposing services externally without manually creating load balancers.

## 18. What is the difference between Kubernetes Services and Ingress?
A Service defines stable networking and optionally a load balancer for a set of pods. Ingress manages external HTTP(S) routing to services. We used Ingress with the AWS Load Balancer Controller to centralize routing rules for multiple microservices.

## 19. How do you manage secrets in EKS?
We stored sensitive configuration in AWS Secrets Manager and referenced them via IAM roles for service accounts. This approach kept secrets out of container images and version control.

## 20. How does EKS support multi-AZ deployments?
EKS automatically creates the control plane across multiple Availability Zones. We deployed worker nodes in at least two AZs so that if one zone had issues, our applications remained available.

## 21. What is an EKS managed policy?
These are AWS-maintained IAM policies that grant the necessary permissions for cluster operations. Using them reduces the chance of missing required permissions when spinning up new clusters.

## 22. What is the purpose of `kubeconfig`?
The `kubeconfig` file stores cluster connection details and credentials used by `kubectl`. We versioned sanitized kubeconfig templates in our repository so new team members could quickly configure access.

## 23. How do you access the Kubernetes dashboard in EKS?
We deployed the dashboard with an admin service account and accessed it via `kubectl proxy` during troubleshooting. Access was limited to VPN users to ensure security.

## 24. What is the role of CloudFormation in EKS?
CloudFormation allows us to define EKS clusters, node groups, and supporting resources as code. At a previous job, we used CloudFormation templates to create identical clusters for development, staging, and production environments.

## 25. How can you run stateful workloads in EKS?
We used StatefulSets along with Amazon EBS volumes for persistent storage. This setup supported databases like PostgreSQL, where each replica needed dedicated storage.

## 26. How do you configure network policies?
We installed the Calico CNI to enforce Kubernetes NetworkPolicy rules. This let us limit pod-to-pod communication so that only required services could talk to each other.

## 27. What is the purpose of AWS App Mesh with EKS?
App Mesh provides a service mesh that standardizes traffic routing and telemetry. We integrated App Mesh when we needed fine-grained control over retries and to gather detailed metrics on service-to-service calls.

## 28. How do you integrate CI/CD with EKS?
Our pipelines built Docker images, pushed them to Amazon ECR, and used Helm charts to deploy to EKS. After each merge, automated tests verified the deployment in a staging cluster before promoting to production.

## 29. What is the difference between pods and deployments?
A pod is a group of one or more containers that share networking and storage. A deployment manages replica sets to ensure the desired number of pods are running. Deployments also handle rolling updates, which we relied on for zero-downtime releases.

## 30. How do you upgrade worker nodes?
We created a new node group with the target AMI version, drained pods from old nodes, and then terminated them. This rolling approach minimized disruption during upgrades.

## 31. How can you enable encryption of Kubernetes secrets at rest?
We configured envelope encryption with AWS KMS keys so secrets stored in etcd were encrypted. This met compliance requirements for storing sensitive customer data.

## 32. What is a service account in EKS?
A service account represents an identity for pods. We associated IAM roles with specific service accounts to grant pods direct access to S3 or DynamoDB without distributing long-lived credentials.

## 33. How do you configure horizontal pod autoscaling?
After deploying the Kubernetes Metrics Server, we created HorizontalPodAutoscaler objects to scale deployments based on CPU or custom metrics. This helped maintain performance during traffic surges.

## 34. What does the `eksctl` tool do?
`eksctl` is a CLI that simplifies creating and managing EKS clusters. We used it for quick provisioning in development environments and to experiment with different cluster configurations.

## 35. How do you set up Amazon ECR with EKS?
Build your container images, push them to an ECR repository, and then reference that repository in your Kubernetes manifests. We configured lifecycle policies in ECR to automatically expire old images and save storage costs.

## 36. How does EKS handle persistent storage?
EKS supports persistent volumes through storage classes for services like Amazon EBS, EFS, and FSx. We often used EFS for shared storage across multiple pods and EBS for performance-sensitive databases.

## 37. What are EKS add-ons?
EKS add-ons provide managed installation and upgrades for common cluster components. Using add-ons meant we didn't need to track YAML manifests for kube-proxy or CoreDNS, saving maintenance effort.

## 38. What is the difference between EKS and self-managed Kubernetes?
With EKS, AWS runs the control plane and integrates deeply with IAM and VPC networking. In self-managed clusters on EC2, you handle everything yourself, including etcd backups and master node upgrades. We chose EKS to reduce operational overhead and leverage AWS support.

## 39. How do you configure custom domains with Ingress?
We created an Ingress resource annotated for the AWS ALB Ingress Controller and then updated Route 53 records to point our custom domain to the ALB DNS name. This let us expose services with friendly URLs.

## 40. How do you manage cluster networking with CNI plugins?
EKS uses the Amazon VPC CNI plugin by default, giving each pod a VPC IP address. For network policies, we deployed Calico alongside the VPC CNI so we could enforce security rules between pods.

## 41. What is the AWS Node Termination Handler?
It listens for EC2 Spot Instance interruptions or maintenance events and gracefully drains the node. This prevented abrupt pod terminations when AWS reclaimed spot capacity in our cost-sensitive environments.

## 42. How do you use AWS PrivateLink with EKS?
PrivateLink exposes EKS API endpoints within your VPC via interface endpoints. We used it to secure cluster management traffic without routing through the public internet.

## 43. What is the difference between Spot Instances and On-Demand in EKS?
Spot Instances offer lower cost but can be interrupted, so we scheduled stateless workloads on them. Critical services ran on On-Demand or Reserved Instances for guaranteed availability.

## 44. How do you enforce pod security policies?
In newer Kubernetes versions, we use the Pod Security Admission controller to apply predefined security levels. We also leveraged tools like Kyverno to validate that pods run with minimal privileges.

## 45. How do you secure API server access?
We restricted access to the cluster endpoint with security groups and PrivateLink. IAM authentication controlled who could run `kubectl`, and we required MFA for elevated roles.

## 46. What is the role of kube-proxy in EKS?
Kube-proxy configures networking rules on each node so traffic sent to a Service IP reaches the correct pod. We tuned kube-proxy to use IP tables mode for better scalability.

## 47. How do you back up and restore EKS clusters?
We used Velero to back up Kubernetes resources to S3 and to take volume snapshots. Restoring to a new cluster was as simple as running `velero restore` with the appropriate backup name.

## 48. What is the purpose of the Amazon VPC CNI plugin?
The VPC CNI assigns VPC IP addresses to pods and attaches Elastic Network Interfaces (ENIs) to nodes. This allowed our pods to communicate directly with AWS services that only permit traffic from VPC addresses.

## 49. How do you leverage Helm in EKS?
Helm packages applications as charts. We stored chart versions in a private repo and used them in our CI/CD pipeline to deploy standardized services such as NGINX Ingress or custom applications.

## 50. How can you integrate EKS with other AWS services?
EKS integrates with IAM for authentication, CloudWatch for logging and metrics, Load Balancers for ingress, ECR for images, and KMS for secret encryption. In practice, we combined these services to build secure and observable production platforms.

