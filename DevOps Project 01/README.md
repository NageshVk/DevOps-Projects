# Kubernetes End to End Project on EKS(Amazon Kubernetes Service)
## Project Title: Deploying 2048 Game App on Amazon EKS

## **Prerequisites**:

**kubectl** – A command line tool for working with Kubernetes clusters. For more information, see Installing or updating kubectl.
``` shell
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

**eksctl** – A command line tool for working with EKS clusters that automates many individual tasks. For more information, see Installing or updating.
``` shell
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

**AWS CLI** – A command line tool for working with AWS services, including Amazon EKS. For more information, see Installing, updating, and uninstalling the AWS CLI in the AWS Command Line Interface User Guide. After installing the AWS CLI, we recommend that you also configure it. For more information, see Quick configuration with aws configure in the AWS Command Line Interface User Guide.
``` shell
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin --update
aws configure
```

## Getting Started

### Step 1: IAM Configuration for EKS Cluster
- Create a role `eks-admin` with `AmazonEKSClusterPolicy`.
- Generate Security Credentials: Access Key and Secret Access Key.

### Step 2: Setup EKS Cluster
- Create an EKS cluster from AWS mangement console.
- Attach the role `eks-admin ` to the cluster.
- Everthing else can be left with default settings.

- Click 'Create'. This process will take 10-12 minutes. Wait till your cluster shows up as Active. Now you can add Node Group to your cluster.

### Step 3: IAM configuration for Node Group
- Create a user `eks-nodegrp` with `AmazonEC2ContainerRegistryReadOnly`,`AmazonEKS_CNI_Policy`and `AmazonEKSWorkerNodePolicy`

### Step 4: Add Node Groups to our cluster
- Open the cluster > Compute > Add NodeGroup
- Select the role you already created.
- Enable SSH access. Choose a security group which allwos 22, 80, 8080

- Node group creation may take 2-3 minutes

### Step 5: Authenticate to this cluster
- kubeconfig configuration allows you to connect to your cluster using the kubectl command line.
```shell
aws eks update-kubeconfig --region region-code --name eks-cluster
```

### Step 6: Create the pod

```
apiVersion: v1
kind: Pod
metadata:
   name: 2048-pod
   labels:
      app: 2048-ws
spec:
   containers:
   - name: 2048-container
     image: blackicebird/2048
     ports:
       - containerPort: 80
```
- apply the config file to create the pod
kubectl apply -f 2048-pod.yaml

- view the newly created pod
kubectl get pods

### Step 7: Setup Load Balancer Service

```
apiVersion: v1
kind: Service
metadata:
   name: mygame-svc
spec:
   selector:
      app: 2048-ws
   ports:
   - protocol: TCP
     port: 80
     targetPort: 80
   type: LoadBalancer
```
- apply the config file
kubectl apply -f mygame-svc.yaml

- view details of the modified service
kubectl describe svc mygame-svc

### Step 8: Access the pod via Load Balancer
curl <LoadBalancer_Ingress>:<Port_number>
- Get the ELB DNS from the EC2 Management console.


### Alternative method of creating EKS Cluster
-Alternatively, the cluster can also be created via command line tool.
``` shell
eksctl create cluster --name eks-cluster --region ap-south-1 --node-type t2.medium --nodes-min 2 --nodes-max 2
```

### Cleanup
- To delete the EKS cluster:
``` shell
eksctl delete cluster --name eks-cluster --region ap-south-1
```
