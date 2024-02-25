# nithirty-KubernetesWithHelm
Hands-On  Kubernetes to deploy a Microservice architecture  Monitor cluster .



**Steps to create K8 cluster using kops**

Launch and ec2 instance on aws to bootstrap K8 Cluster
ssh to instance




create user(lets name it "kops") in aws console with programmatic access
create group with below-mentioned policies and kops user attached to it.

AmazonEC2FullAccess
AmazonRoute53FullAccess
AmazonS3FullAccess
IAMFullAccess
AmazonVPCFullAccess
AmazonSQSFullAccess
AmazonEventBridgeFullAccess

# configure the aws client to use your new IAM user
aws configure           # Use your new access and secret key here
aws iam list-users      # you should see a list of all your IAM users here

# Because "aws configure" doesn't export these vars for kops to use, we export them now
export AWS_ACCESS_KEY_ID=$(aws configure get aws_access_key_id)
export AWS_SECRET_ACCESS_KEY=$(aws configure get aws_secret_access_key)


**Install kops** 
Ref --> https://kubernetes.io/docs/tasks/tools/ 

download :
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
validate :
curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
kubectl: OK

Install :
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

Test :
kubectl version --client

**Install kops** 
#Ref--> https://kops.sigs.k8s.io/getting_started/aws/
curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops
sudo mv kops /usr/local/bin/kops


**Create Cluster**

Create S3 Bucket
export NAME=fleetman.k8.local
export KOPS_STATE_STORE=s3://kops-state-storage-ssd


Check the Datacenter in AZ 

aws ec2 describe-availability-zones --region us-east-1
AZ --> us-east-1a,us-east-1b,us-east-1c

kops create cluster --zones us-east-1a,us-east-1b,us-east-1c $NAME --yes

NOTE : First create private hosted zone in Aws Route53  with name k8.local then use below command to create the cluster
this is called gossip dns it more ref--> https://kops.sigs.k8s.io/gossip/ 


kops create cluster --zones us-east-1a,us-east-1b,us-east-1c -- dns private $NAME --yes







