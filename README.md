# DevOpsProject

Installing Docker on ec2 Machine and creating NginxAlpine:3.10 image

1.	Login into the ec2-machine
2.  sudo yum update -y
3.  sudo yum install docker -y
4.  sudo service docker start
5.	For creating the Dockerfile, pull the contents of this repository
6.  docker build -t swetarathore/alpinenginx:1.0 .
7.  docker push swetarathore/alpinenginx:1.0
8.  sudo docker run -it -d swetarathore/alpinenginx:1.0
9.  sudo docker ps -a
10. sudo docker exec -it 9701bfb75cc4 /bin/sh --this command is to check if the image is running properly

Creating Kuberenetes Cluster and installing kubectl

1. Create a t2.micro AWS ec2 machine
2. Check if aws-cli was installed. Mostly it is pre-installed in AWS EC2 Machines.

    aws --version
3.	Install kubectl on Linux Machine

    curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.17.9/2020-08-04/bin/linux/amd64/kubectl
    
    chmod +x ./kubectl
    
    sudo mv ./kubectl /usr/local/bin
    
    kubectl version --short --client        //check if installed properly
    
4.	Create EKS Cluster IAM Role - this can be done through IAM Roles or through cloudformation. I did it through IAM Roles.

    Open the IAM console at https://console.aws.amazon.com/iam/.
    
    Choose Roles, then Create role
    
    Choose EKS from the list of services, then EKS - Cluster for your use case, and then Next: Permissions.
    
    Choose Next: Tags.
    
    Choose Next: Review.
    
    For Role name, enter a unique name for your role, such as eksClusterRole, then choose Create role.
    
5.	Create Amazon EKS cluster VPC - I created cluster VPC with public and private subnets to avoid any permissions issue.

    Open the AWS CloudFormation console at https://console.aws.amazon.com/cloudformation.
    
    From the navigation bar, select a Region that supports Amazon EKS.
    
    Choose Create stack, With new resources (standard).
    
    For Choose a template, select Specify an Amazon S3 template URL.
    
    Paste the URL into the text area and choose Next: https://amazon-eks.s3.us-west-2.amazonaws.com/cloudformation/2020-08-12/amazon-eks-vpc-private-subnets.yaml
    
    Choose Next as the default value for VPC Block provides enough IP addresses for most implementations
    
    On the Review page, choose Create.
    
6.	Create your Amazon EKS cluster through AWS Console using the previously created IAM Role and VPC. Also choose the Cluster End point access as public and private.

7.	Move to the Kubernetes machine's terminal and update the kubectl config. Use appropriate region and name of the Cluster created in the previous step

    aws eks --region us-east-2 update-kubeconfig --name Nginx
    
8. Test your configuration

    kubectl get svc


Running containers/pods in Kuberenetes

1. In EKS Cluster, move to computer section and create Worker Nodes
2. Install Docker in your Kuberenetes Machine in order to access the public repository
3. Login to Docker using your credentials

   sudo docker login
   
4. Create a deployment for a pod

    kubectl create deployment --image=swetarathore/alpinenginx:1.0  my-nginx-pod
    
5.  Check if the pod is running

    kubectl get pods
    
6.  Scale your deployment to create 3 nginx pods 

    kubectl scale deployment --replicas 3 my-nginx-pod
    
7.  Expose the deployment to the loadbalancer

    kubectl expose deployment my-nginx-pod --port=80 --type=LoadBalancer
    
8.  To check if the nginx is running, go to AWS EC2 Console -> Loadbalancers -> copy the DNS and paste it into the broswer

IDEA FOR STATIC FILE IMPLEMENTATION

We can use the command " kubectl get pods -A -o=wide" for getting the IP of the pod and write into a file say "StaticIPFile". This file can be placed into a shared volume which is accessible by all the pods. The pods can register their IPs into the file as soon as they are created and also gets destroyed as soon as the pods are deleted.





