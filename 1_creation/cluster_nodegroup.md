Certainly! Below is the enhanced documentation with command references included for each step:

---

## EKS Cluster & Node Groups Setup Guide

### Step-00: Introduction

This section provides an overview of the core objects in Amazon EKS and outlines the actions we'll perform to set up the cluster and node groups.

#### EKS Core Objects:
- **Control Plane**: The management infrastructure provided by Amazon EKS, responsible for managing the Kubernetes control plane.
- **Worker Nodes & Node Groups**: These are the compute resources where your Kubernetes applications run. Worker nodes can be managed by node groups.
- **Fargate Profiles**: Serverless compute engine for containers that completely removes the need to manage servers. Not directly relevant to this setup.
- **VPC**: Virtual Private Cloud where EKS resources are deployed. It provides network isolation and security for your EKS cluster.

### Step-01: Create EKS Cluster using eksctl

In this step, we use `eksctl` to create an Amazon EKS cluster.

#### Command:
```bash
eksctl create cluster --name=rajibuldemo1 \
                      --region=us-east-1 \
                      --zones=us-east-1a,us-east-1b \
                      --without-nodegroup
```

#### Why it's needed:
- We need an EKS cluster to deploy and manage Kubernetes applications.
- `eksctl` is a command-line tool that simplifies the creation and management of EKS clusters.

### Step-02: Create & Associate IAM OIDC Provider for our EKS Cluster

This step creates and associates an OIDC identity provider with the EKS cluster.

#### Command:
```bash
eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster rajibuldemo1 \
    --approve
```

#### Why it's needed:
- To enable IAM roles for Kubernetes service accounts (IRSA) on our EKS cluster, we must create and associate an OIDC identity provider.
- IRSA allows Kubernetes pods to assume IAM roles, providing fine-grained access control to AWS resources.

### Step-03: Create EC2 Keypair

We create an EC2 key pair that will be used for SSH access to the EKS worker nodes.

#### Why it's needed:
- We need an SSH key pair to securely access the EC2 instances running in our EKS node groups.

### Step-04: Create Node Group with Additional Add-Ons in Public Subnets

This step creates an EKS node group with additional add-ons in public subnets.

#### Command:
```bash
eksctl create nodegroup --cluster=rajibuldemo1 \
                       --region=us-east-1 \
                       --name=rajibuldemo1-ng-public1 \
                       --node-type=t3.medium \
                       --nodes=2 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \
                       --ssh-access \
                       --ssh-public-key=kube-demo \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access
```

#### Why it's needed:
- Node groups are groups of EC2 instances (nodes) that run Kubernetes pods.
- The add-ons specified in this step automatically create IAM policies required for additional functionalities like SSH access, external DNS access, full ECR access, etc.


Here is a breakdown of the options used in the command:
--cluster: Specifies the name of the Amazon EKS cluster to which the node group will be added.
--region: Specifies the AWS region in which the cluster is located.
--name: Specifies the name of the node group.
--node-type: Specifies the EC2 instance type for the worker nodes in the node group.
--nodes: Specifies the number of worker nodes to launch in the node group.
--nodes-min: Specifies the minimum number of worker nodes that the node group can scale in to.
--nodes-max: Specifies the maximum number of worker nodes that the node group can scale out to.
--node-volume-size: Specifies the size of the root device disk for the worker nodes in the node group.
--ssh-access: Enables SSH access to the worker nodes in the node group.
--ssh-public-key: Specifies the name of the public key to use for SSH access to the worker nodes.
--managed: Specifies that the node group is managed by Amazon EKS.
--asg-access: Enables access to the Auto Scaling group associated with the node group.
--external-dns-access: Enables access to external DNS resources.
--full-ecr-access: Enables full access to Amazon ECR.
--appmesh-access: Enables access to AWS App Mesh.
--alb-ingress-access: Enables access to the ALB Ingress Controller.

### Step-05: Verify Cluster & Nodes

This step verifies the setup by checking various components like clusters, node groups, EC2 instances, IAM policies, and node groups.

#### Commands:
- List EKS Clusters:
```bash
eksctl get cluster
```
- List NodeGroups in a Cluster:
```bash
eksctl get nodegroup --cluster=rajibuldemo1
```
- List Nodes in the current Kubernetes cluster:
```bash
kubectl get nodes -o wide
```
- View current kubectl context:
```bash
kubectl config view --minify
```

#### Why it's needed:
- Verification ensures that the setup was successful and all components are functioning as expected.
- It helps in troubleshooting if there are any issues with the setup.

### Step-06: Update Worker Nodes Security Group to Allow All Traffic

In this step, we update the security group associated with the worker nodes to allow all traffic.

#### Why it's needed:
- Allowing all traffic may be necessary depending on the requirements of the applications running on the worker nodes.
- It ensures that network traffic is not blocked by security group rules.

### Additional References

This section provides links to additional documentation for further reading and understanding of concepts related to Amazon EKS.

#### Why it's needed:
- Additional references provide more detailed information about specific topics related to EKS setup and management.
- They serve as resources for learning and troubleshooting issues during setup and operation.

---

### EKS Cluster Pricing

#### Step-01: Very Important EKS Pricing Note

- EKS is not free and does not offer a free tier like some other AWS services.
- There are costs associated with running EKS clusters and worker nodes.

#### EKS Cluster Pricing

- You are charged $0.10 per hour for each Amazon EKS cluster.
- This equates to $2.4 per day and approximately $72 per month for a single EKS cluster.

#### EKS Worker Nodes Pricing - EC2

- Worker nodes, typically EC2 instances, incur charges based on the resources utilized.
- For example, a T3 Medium server in the N. Virginia region costs $0.0416 per hour, roughly $1 per day, and $30 per month.
- If you continuously run 1 EKS cluster and 1 t3.medium worker node for a month, the bill would be around $102 to $110.
- For a 5-day course, running 1 EKS cluster and 2 t3.medium worker nodes continuously would cost approximately $25.

#### EKS Fargate Profile

- AWS Fargate pricing is calculated based on the vCPU and memory resources used by your containers from start to termination.
- Pricing details are available on the AWS Fargate pricing page.
- EKS support for Fargate is available in specific regions: us-east-1, us-east-2, eu-west-1, and ap-northeast-1.

#### Important Notes

- **Note-1:** Ensure you delete and recreate clusters and worker nodes as needed, especially if using a personal AWS account.
- **Note-2:** Unlike regular EC2 instances, you cannot stop EC2 instances within a Kubernetes cluster. Therefore, delete worker nodes (node groups) when not in use during the learning process.

---

This documentation outlines the pricing details for Amazon EKS clusters, worker nodes, and Fargate profiles. It also provides important notes regarding management and cost optimization.


# EKS Cluster Pricing

## Step-01: Very Important EKS Pricing Note
- EKS is not free, unlike many other AWS services.
- There is no free-tier offering for EKS.

### EKS Cluster Pricing
- The cost is $0.10 per hour for each Amazon EKS cluster.
- This translates to $2.4 per day and approximately $72 for 30 days.

### EKS Worker Nodes Pricing - EC2
- Charges are incurred for AWS resources such as EC2 instances or EBS volumes.
- For example, a T3 Medium server in the N.Virginia region costs $0.0416 per hour, roughly $1 per day, and $30 per month.
- If running 1 EKS Cluster and 1 t3.medium worker node continuously for 1 month, the bill would be around $102 to $110.
- If running 1 EKS Cluster and 2 t3.medium Worker nodes continuously for 5 days, it would cost approximately $25.

### EKS Fargate Profile
- AWS Fargate pricing is based on the vCPU and memory resources utilized from the start of downloading the container image until the EKS Pod terminates.
- For detailed pricing, refer to the [AWS Fargate pricing page](https://aws.amazon.com/fargate/pricing/).
- Amazon EKS support for AWS Fargate is available in us-east-1, us-east-2, eu-west-1, and ap-northeast-1 regions.

### Important Notes
- **Important Note-1:** For personal AWS Accounts, ensure timely deletion and recreation of clusters and worker nodes as required.
- **Important Note-2:** Unlike regular EC2 Instances, EC2 Instances within Kubernetes clusters cannot be stopped. Thus, it is necessary to delete the worker nodes (Node Group) when not in use during the learning process.