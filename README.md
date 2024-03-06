# Provision an EKS Cluster [DATE 06-03-24]
This repo contains Terraform configuration files to provision an EKS cluster on AWS.

## Pre-requisites

- ### Install terraform
https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli
```
$ terraform --version
Terraform v1.7.4
on linux_amd64
```

- ### Install & configure aws-cli with acceess key & 
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
```
$ aws --version
aws-cli/2.15.25 Python/3.11.8 Linux/6.5.0-1014-aws exe/x86_64.ubuntu.22 prompt/off

$ aws configure
AWS Access Key ID [None]: AKIAXYO2************
AWS Secret Access Key [None]: 9FPlDlgs5hsb8******************
Default region name [None]: ap-south-1
Default output format [None]: 

$ cat ~/.aws/config 
[default]
region = ap-south-1
$ cat ~/.aws/credentials 
[default]
aws_access_key_id = AKIAXYO2************
aws_secret_access_key = 9FPlDlgs5hsb8******************
```

- ### Install aws-iam-authenticator
https://github.com/kubernetes-sigs/aws-iam-authenticator
```
~/Downloads$ wget https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.6.14/aws-iam-authenticator_0.6.14_linux_amd64
~/Downloads$ chmod +x aws-iam-authenticator_0.6.14_linux_amd64 
~/Downloads$ sudo cp aws-iam-authenticator_0.6.14_linux_amd64 /usr/local/bin/aws-iam-authenticator

$ aws-iam-authenticator version
{"Version":"0.6.14","Commit":"b978afae7be72c6c27f8ed2000685b1e9268cd0e"}
```

- ### Install kubectl
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
```
$ kubectl version
Client Version: v1.29.2
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
```


## Provision the cluster
```
~/Documents/GitHub/eks-terraform$ terraform init

~/Documents/GitHub/eks-terraform$ terraform apply
# when asked for confirmation to perform action, confirm by entering "yes"
```

## Access the cluster
```
:~/Documents/GitHub/eks-terraform$ terraform output -raw cluster_name
terraform-eks-6Dnf8rr2

~/Documents/GitHub/eks-terraform$ terraform output -raw cluster_endpoint
https://A28539FDE84F67C907624F8DE4292D0F.yl4.ap-south-1.eks.amazonaws.com

~/Documents/GitHub/eks-terraform$ terraform output -raw region
ap-south-1

~/Documents/GitHub/eks-terraform$ aws eks --region $(terraform output -raw region) update-kubeconfig --name $(terraform output -raw cluster_name)
Added new context arn:aws:eks:ap-south-1:533570549156:cluster/terraform-eks-6Dnf8rr2 to /home/ubuntu/.kube/config

~/Documents/GitHub/eks-terraform$ kubectl cluster-info
Kubernetes control plane is running at https://A28539FDE84F67C907624F8DE4292D0F.yl4.ap-south-1.eks.amazonaws.com
CoreDNS is running at https://A28539FDE84F67C907624F8DE4292D0F.yl4.ap-south-1.eks.amazonaws.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

~/Documents/GitHub/eks-terraform$ kubectl get no -o wide
NAME                                        STATUS   ROLES    AGE   VERSION               INTERNAL-IP   EXTERNAL-IP   OS-IMAGE         KERNEL-VERSION                  CONTAINER-RUNTIME
ip-10-0-1-83.ap-south-1.compute.internal    Ready    <none>   16m   v1.27.9-eks-5e0fdde   10.0.1.83     <none>        Amazon Linux 2   5.10.209-198.858.amzn2.x86_64   containerd://1.7.11
ip-10-0-3-131.ap-south-1.compute.internal   Ready    <none>   17m   v1.27.9-eks-5e0fdde   10.0.3.131    <none>        Amazon Linux 2   5.10.209-198.858.amzn2.x86_64   containerd://1.7.11
ip-10-0-3-169.ap-south-1.compute.internal   Ready    <none>   17m   v1.27.9-eks-5e0fdde   10.0.3.169    <none>        Amazon Linux 2   5.10.209-198.858.amzn2.x86_64   containerd://1.7.11
```
## Clean up
```
~/Documents/GitHub/eks-terraform$ terraform destroy
# when asked for confirmation to perform action, confirm by entering "yes"
```

