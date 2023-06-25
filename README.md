# swe645
In this project swe645 HW2 has been dockerized and then deployed to self hosted ec2 cluster in aws.
1) Rancher Node: https://ec2-34-233-228-98.compute-1.amazonaws.com/dashboard/
2) Application URL: https://ec2-3-222-240-66.compute-1.amazonaws.com/SurveyForm/
3) Jenkins URL: http://34.228.87.20:8080/
*** Make Sure that EC2 IP's are whitelisted for communication with each other. ***
# Docker installation Steps:
1) sudo apt update
2) sudo apt install docker.io

# Rancher Setup:
1) Spin up EC2 host with atlease t2.medium as rancher consumes more memory it might crash in free tier.
2) Install docker in EC2 host created for Rancher For this usecase Ubuntu image is used.
3) Execute: sudo docker run --privileged=true -d --restart=unles-sstopped -p 80:80 -p 443:443 rancher/rancher
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/e9cb964a-4198-42d9-bb83-812b88166371)
4) Local cluster is created by default. We cannot schedule our application pods in that as it has taints to it
5) Create another Ec2 machine with tier t2.medium this will be used as a cluster for deploying application code.
6) In Racher UI navigate to cluster management for creating new cluster. There are various options present We can use different cloud providers or even use an ec2 iam user for managing the cluster. For our use case we have used custom one.
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/f837fd01-a333-4814-8971-f778ce194fc8)
7) In case of custom when the cluster is created in order to make it active we have to register it with the Rancher node which was previously created. For this we have to execute the registration command in new EC2 node provided in Rancher UI. Once this is done cluster will appear as active in 4-5 mins.
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/e3163382-dafb-42f4-9ac5-de6e5d37b506)

# Jenkins Setup:
1) Spin up an EC2 container for installing jenkins.
2) Install docker using command: sudo apt install docker.io
3) Install java
4) Use following page for installing jenkins https://www.digitalocean.com/community/tutorials/how-to-install-jenkins-on-ubuntu-22-04
5) After jenkins is up and started install kubectl utility for interacting with cluster.
6) Create .kube directory in /home/jenkins. This will store the config file for connecting to cluster. DOwnload kubeconfig file from Rancher cluster UI.
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/82c9cb60-cf34-42d3-8420-640aa8624b2e)
7) Once this is done connection with the cluster can be tested using kubectl command ex: kubectl get nodes
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/6f2ecebf-9d92-401e-8b5d-e833e81d6253)
8) In order to push image from jenkins server to dockerhub we need to add jenkins user to docker group. sudo usermod -a -G docker jenkins
9) Credentials to be configured inside Manage Credentials in jenkins for security reasons.
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/2f8189fe-c250-4b9c-a7f1-a34f215bd073)
10) After the configuration for jenkins server side is done then we can simply create a new pipeline script from UI. In our case we have a cron scheduler that polls changes in github repository and if identified then triggers the job.
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/1be34665-957b-45f2-add6-85061428c7f8)
11) Jenkinsfile is for pipeline is in code repository itself.

# Application Setup
1) All the kubernets related configuration are present inside kubernetes/
2) ingress.yaml defines the ingress for communicating with the service.
3) deployment.yaml is used for fetching the image from hub.docker.com and then create number of pods as specified in replica. In our case it is 3.
4) service.yaml is used for connecting deployment to other kubernetes components. Once service is created then ingress can send traffic to application pods.
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/bda3f547-c17f-4ad7-bddb-80f263ce2279)
5) Base image used for creating docker image is tomcat:9.0-jdk15

# Working Screenshots
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/4e3ea999-c2dc-486f-851b-a1267080ce39)
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/03cb12e9-58e3-4f34-9099-b8d34855e29e)
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/c63001fc-a561-4b26-9795-6e86fc7a3d12)
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/5d2bcfee-6947-487b-ae0c-54c030cfa1b9)
![image](https://github.com/dev-founder-titan/swe645/assets/79055244/653612e2-a42a-42be-bcc2-96184ef9de10)
