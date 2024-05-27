# Kubernetes-app
in this project we are going to deploy web application on kubernetes cluster we already push images to our dockerhub repo we will run these images on k8s cluster for production, we will use kops to setup our cluster and contranized our images

# steps to follow
- create our cluster '1 master node, 2 worker nodes'
  kops create cluster --name=kubevpro.groophy.in \ 
  --state=s3://vprofile-kop-states --zones=us-east-2a,us-east-2b \ 
  --node-count=2 --node-size=t3.small --master-size=t3.medium --dns-zone=kubevpro.groophy.in \ 
  --node-volume-size=8 --master-volume-size=8

- To launch our cluster
  kops update cluster --name kubevpro.groophy.in --state=s3://vprofile-kop-states --yes --admin 

- To validate our claster
  kops validate cluster --name kubevpro.groophy.in --state=s3://vprofile-kop-states  

- Now time for our application deployed in our cluster first db, we have to create volume for our db pod so it can store sql data that stored in '/var/lib/mysql' into EBS volume
 'aws ec2 create-volume --availability-zone=us-east-1a --size=3 --volume-type=gp2'

- After this we have to label our two worker nodes first node with 'zone=us-east-1a', second node 'zone=us-east-1b

- Now time for configuration and yaml files, first we have to create our secret file to store encoded values of passwords for db and rmq which are located in application properties ..Encode==>'echo -n 
 "pass value" | base64', ..Create file==> k create -f app-secret.yml 

- For db deployment file i am going to give a label for the svc definition file of a type cluster ip and the svc is going to route the request to pods that have the same label, we will create db container 
  with db image we created before and push it to docker repo from containerization project, we will schedule our pod on the same node we created the volume on by mention node selector

- After this we will create svc of type cluster ip for our db so that our app pod can access it 

- Now create our deployment file for memecashe and service of cluster ip for our memecashe so it can be accessable

- For rabbitmq deployment file we will mention env var from our application properties and secrets file 'username and pass' and create our service to be accessable

- For tomcat app deploy we will mention our image from our dockerhub repo and create svc with load balancer type to access our app from internet
