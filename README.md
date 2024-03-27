# **Kubernetes Vprofile Test app**

## Test Project with Kubernetes using KOPS on EC2 

### Configuration
1. create S3 bucket (example: **s3://vprofile-kops-state-3** )
2. create DNS registry on Route 53 with a full name from Website DNS setting (example: **kubevpro.okdeops.de**) (I had 4 DNS Records)
3. create 4 NS Record DNS on Domain resgisty
4. create EC2 instance t2.micro is enough to install KOPS with SSH
5. run this Command on KOPS instance : 
   - **kops create cluster --name=kubevpro.okdeops.de \
     --state=s3://vprofile-kops-state-3 --zones=us-east-1a,us-east-1b \
     --node-count=2 --node-size=t2.small --master-size=t2.medium --dns-zone=kubevpro.okdeops.de \
     --node-volume-size=8 --control-plane-volume-size=8**
6. run this command to Edit config file :

   **kops edit cluster --name kubevpro.okdeops.de --state s3://vprofile-kops-state-3**
7. edit these lines to change the ETCD Volume on Master Node (**Control Plane Node**) and Work Node like this: 

    #### etcdClusters:
    - etcdMembers:
     - instanceGroup: master-us-east-1a
     -  name: a
     -  volumeType: gp3
     -  volumeSize: 8
    - name: main
    
8. create the Resourses (** Control Plane Node & 2 Work Nodes & Security Group & Auto Scaling Group **) on AWS with command:
   **kops update cluster --name kubevpro.okdeops.de --state=s3://vprofile-kops-state-3 --yes --admin**
9. wait about 10 min and Validate the Resourses to be Ready with this command:
   **kops validate cluster --name kubevpro.okdeops.de --state=s3://vprofile-kops-state-3**
10. Create a Volume for DB with command:
   **aws ec2 create-volume --availability-zone us-east-1a --size 3 --volume-type gp3 --tag-specifications 'ResourceType=volume,Tags=[{Key=KubernetesCluster,Value=kubevpro.okdeops.de}]'**
11. change the Label of worker nodes :
   **kubectl label node i-instanceID zone=us-east-1b**
   **kubectl label node i-instanceID zone=us-east-1a**
12. create the Project after a pull from github to Kops instace :
   **kubectl create -f .**
13. after you finished delete everything : 
   **kubectl delete -f .**
14. Delete all the resources (Control Plane and Worker nodes & ...) :
   **kops delete cluster --name=kubevpro.okdeops.de --state=s3://vprofile-kops-state-3 --yes**



![alt text](https://github.com/okhouja/Kube-Vprofile-app/blob/main/Vprofile_HomePage.jpg?raw=true)

![alt text](https://github.com/okhouja/Kube-Vprofile-app/blob/main/Vprofile_RabbitMQ_Page.jpg?raw=true)

![alt text](https://github.com/okhouja/Kube-Vprofile-app/blob/main/Vprofile_userPage_1.jpg?raw=true)

![alt text](https://github.com/okhouja/Kube-Vprofile-app/blob/main/Vprofile_userPage_2.jpg?raw=true)