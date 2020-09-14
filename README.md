# How to deploy wordpress on Google Cloud Platform
# Overview of the project
This project is for deploying the front end wordpress application on top of Google Cloud Platform along with the back end mysql database over a Virtual Private Network.
# What is Google Cloud Platform?
Before going into the project, lets look into what a GCP is.
# Project Description
* Create multi projects with dev and prod.
* Create a VPC network in Singapore region for dev project.
* Create VPC network in US region for prod project.
* Connect both the vpc network with VPC peering.
* Create a Kubernetes Cluster in dev project and launch a wordpress application with Load Balancer.
* Create a SQL server in prod project and create a database.
* Connect the SQL DATABASE with the application launched in the kubernites cluster.

## Step 1: Create two projects: `devproject` for developers and `prodproject` for the production
Creating one project for the developer `devproject`.
![devproject](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ devproject.png)
Creating another project for the production `prodproject`.
![ prodproject](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ prodproject.png)

## Step 2: Create VPC network
Firstly we create a VPC network on `devproject`
Name: `dev-vpc`
![ dev-vpc-name](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ dev-vpc-name.png)
New subnet:
Name: `lab-dev`
Region: Singapore i.e., `asia-southeast1`
IP address range: `10.0.1.0/24`
![ dev-vpc-sub](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/dev-vpc-sub
.png)
Click Create 
![ dev-vpc-create](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/dev-vpc-create.png)
`dev-vpc` is now created
![ dev](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/dev.png)


Another we create a VPC network on `prodproject`
Name: `prod-vpc`
![ prod-vpc-name](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ prod-vpc-name.png)
New subnet:
Name: `lab-prod`
Region: US i.e., `us-west1`
IP address range: `10.0.2.0/24`
![ prod -vpc-sub](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/prod-vpc-sub
.png)
Click Create 
![ prod -vpc-create](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ prod -vpc-create.png)
`prod-vpc` is now created
![ prod](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/prod.png)

## Step 3: Create VPC peering on both the networks
One VPC peering on `dev-project`
![ dev-peering](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/dev-peering.png)
Name: 	dev-vpc-peering
Your VPC network: `dev-vpc`
Peering VPC network: Select `In another project`
Project ID: `prodproject-289412`
VPC network name: `prod-vpc`
![ dev-peering-create](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/dev-peering-create.png)
Click create and you will see that the status in `inactive`.
![ dev-peering-status1](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/dev-peering-status1.png)
To make it active we need to create another VPC peering on `prod-project`.

Now, another VPC peering on `dev-project`
![ prod-peering](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/prod-peering.png)
Name: 	prod-vpc-peering
Your VPC network: `prod-vpc`
Peering VPC network: Select `In another project`
Project ID: `devproject-289412`
VPC network name: `dev-vpc`
![ prod -peering-create](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/prod -peering-create.png)
Click create and you will see that the status in `Active`.
![ prod -peering-status](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/prod -peering-status.png)
Now go back to `devproject` , you will see that the status is `Active` now
![ dev-peering-status2](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/dev-peering-status2.png)

## Step 4: Create a Kubernetes Cluster in dev project and launch a wordpress application with Load Balancer
Creating the cluster
![ cluster-create](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cluster-create.png)
Name: `mycluster`
Region: `asia-southeast1`
![ cluster-basic](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cluster-basic.png)
Size of cluster: is used `1`
![ cluster-nodes](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cluster-nodes.png)
Machine Configuration
Series: `N1`
Machine type: `n1-standard-1`
![ cluster-machine](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cluster-machine.png)
Networking
Network: `dev-vpc`
Node subset:`lab-dev`
![ cluster-net](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cluster-net.png)
Click create and Kubernetes cluster is created
![ cluster-created](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cluster-created.png)
After creation, click on `SSH` and click `Run in Cloud Shell`
![ cloudshell](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cloudshell.png)
![ cloudshell1](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cloudshell1.png)
Now deploy the `wordpress` application image
![ cloudshell2](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cloudshell2.png)
To see the running deployments
![ cloudshell3](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cloudshell3.png)
To see the running pods
![ cloudshell4](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cloudshell4.png)
To see the running nodes
![ cloudshell5](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cloudshell5.png)
To see the running services
![ cloudshell6](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cloudshell6.png)
Load balancing is useful in case when large number of users hit the server.
This Load balancing will divide and direct them to different ip address but look and feel will be the same. 
![ cloudshell7](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cloudshell7.png)
Now you can see a new service is created and using this external ip address we can access out `wordpress` applcation.
![ cloudshell8](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/cloudshell8.png)

## Step 5: Create a SQL server in prod project and create a database
Now we need to create a SQL database for out `wordpress` application.
![ sql](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/sql.png)
Selected `MYSQL` database for my project
![ sql-my](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/sql-my.png)
Instance ID: `sql-db`
Set the password (remember for later)
Region: `us-central1’
![ sql-create](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/sql-create.png)
Now the database is created
![ sql-created](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/sql-created.png)
In the connection, we are allowing all the networks
![ sql-net](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/sql-net.png)
In Users, we already have the `root` user
![ sql-user](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/sql-user.png)
Created a database `wpdb` for the `wordpress` application
![ sql-db](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/sql-db.png)
Just to see if the `MYSQL` server is running, connected using the cloud Shell
![ sql-con](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/sql-con.png)
And is working fine
![sql-success](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ sql-success.png)

## Step 6: Connect the SQL DATABASE with the `wordpress` application launched in the kubernites cluster
Using the external ip address, we access our wordpress site
![site1](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ site1.png)
Connect the site with the database
![site2](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ site2.png)
Database Name: `wpdb`
Username: `root`
Password: created earlier
Database Host: It is visible on the mysql
![site3](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ site3.png)
Run the Installation
![site4](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ site4.png)
Provide the necessary information for your account
![site5](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ site5.png)
Login with the given credentials
![site6](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ site6.png)
Now you land onto your wordpress account
![site7](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/ site7.png)

Publish your first blog
![publish](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/publish.png)
After publishing, you can see your Blog
![success](https://github.com/amalk-money/gcp_wordpress/blob/master/screenShots/success.png)

# Thankyou for viewing my work
