# AWS Load Balancer Controller Steps

* In the past, the Kubernetes network load balancer was used for instance targets, but the AWS Load balancer Controller was used for IP targets. 

* With the AWS Load Balancer Controller version 2.3.0 or later, you can create NLBs using either target type. 

* The AWS Load Balancer Controller controller was formerly named the AWS ALB Ingress Controller

Prerequisites:

* An existing Amazon EKS cluster.
* An existing AWS Identity and Access Management (IAM) OpenID Connect (OIDC) provider for your cluster.
* If your cluster is 1.21 or later, make sure that your Amazon VPC CNI plugin for Kubernetes, kube-proxy, and CoreDNS add-ons are at the minimum versions listed in Service account tokens.

To deploy the AWS Load Balancer Controller to an Amazon EKS cluster:

1)Create an IAM policy

  * Download an IAM policy for the AWS Load Balancer Controller that allows it to make calls to AWS APIs on your behalf.


    curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.4.4/docs/install/iam_policy.json
    
 * Create an IAM policy using the policy downloaded in the previous step.


    aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

# Note: Make a note of Policy arn

2)Create an IAM role. Create a Kubernetes service account named aws-load-balancer-controller in the kube-system namespace for the AWS Load Balancer Controller and annotate the Kubernetes service account with the name of the IAM role.

You can use eksctl or the AWS CLI and kubectl to create the IAM role and Kubernetes service account.

eksctl:

    eksctl create iamserviceaccount \
      --cluster=<Provide Ur Cluster-Name> \
      --namespace=kube-system \
      --name=aws-load-balancer-controller \
      --role-name "AmazonEKSLoadBalancerControllerRole" \
      --attach-policy-arn=<Paste the arn that is created in the previous step> \
      --override-existing-serviceaccounts \
      --approve

3)Install Helm

If you're using Linux, install the binaries with the following commands.

    curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 > get_helm.sh
    chmod 700 get_helm.sh
    ./get_helm.sh

See the version of Helm that you installed.

    helm version --short | cut -d + -f 1

4)Install the AWS Load Balancer Controller using Helm V3

a)Add the eks-charts repository.
    
        helm repo add eks https://aws.github.io/eks-charts

b)Update your local repo to make sure that you have the most recent charts.
        
        helm repo update
c) Install 

    helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
      -n kube-system \
      --set clusterName=my-cluster \
      --set serviceAccount.create=false \
      --set serviceAccount.name=aws-load-balancer-controller \
      --set region=us-east-1 \
      --set vpcId=vpc-01b4722d66b73c07a \
      --set image.repository=602401143452.dkr.ecr.us-east-1.amazonaws.com/amazon/aws-load-balancer-controller


Note: Replace "602401143452' and "region-code" with the values for your AWS Region listed in Amazon container image registries.

# Replace region,vpcId and clusterName with your region ,vpcid and clustername

To check in Amazon container image registries in different regions:
    use this link: https://docs.aws.amazon.com/eks/latest/userguide/add-ons-images.html

Verify that the controller is installed.

    kubectl get deployment -n kube-system aws-load-balancer-controller

To uninstall AWS load balancer controller:

    helm uninstall aws-load-balancer-controller -n kube-system 








 








