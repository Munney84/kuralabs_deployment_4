<h1 align=center>Deployment 4 Observations</h1>

# Deploying a flask app with Terraform
## 1. Installing Jenkins Server on an EC2
### 1. Create Amazon EC2 with Ubuntu image
1. select Key pair
2. under network settings create a security group or use an existing group and set ports
3. launch instance
### 2. Install Jenkins on the EC2
1. ssh into EC2 from the location of the Key pair (.pem file): $ssh -i {key}.pem @ubuntu{Public IPv4 DNS}
2. $curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
3.	$echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \ https://pkg.jenkins.io/debian-stable binary/ | sudo tee \ /etc/apt/sources.list.d/jenkins.list > /dev/null
4. $sudo apt-get update
5. $sudo apt-get install jenkins
6. $sudo systemctl start jenkins **
7. $sudo apt install python3
8. $sudo apt install python3-venv

## 2. Install terraform on the Jenkins Server
### 1. Access the CLI on the Jenkins Server
1. To download Terraform zip file Use command: 
<img width="1029" alt="Screen Shot 2022-10-29 at 7 35 41 AM" src="https://user-images.githubusercontent.com/108026310/198829253-acd23d01-e9a9-4936-8445-fcb53fe8904b.png">

2. Install zip using "$sudo apt install unzip" and unzip Terraform file 
<img width="585" alt="Screen Shot 2022-10-29 at 7 38 17 AM" src="https://user-images.githubusercontent.com/108026310/198829332-bebf51b2-2048-40e3-b3ac-1dde7426ac3c.png">
3. Use "$sudo snap install terraform --classic" to install


## 3. Configure credentials on Jenkins
1. Log into Jenkins using the IP address from your defaut VPC and port 8080
2. Navigate to "Manage Jenkins"
<img width="1331" alt="Screen Shot 2022-10-29 at 7 46 54 AM" src="https://user-images.githubusercontent.com/108026310/198829769-0626154c-5042-4df4-ade2-6f76799f7221.png">
3. Navigate to Manage Credentials
<img width="1526" alt="Screen Shot 2022-10-29 at 7 50 04 AM" src="https://user-images.githubusercontent.com/108026310/198829862-41999400-61e0-4518-8ac7-5ac98aeb9d24.png">
4. Select Global 
<img width="915" alt="Screen Shot 2022-10-29 at 7 52 44 AM" src="https://user-images.githubusercontent.com/108026310/198829985-9d972441-337d-4605-b8d3-f1c861eeafdd.png">
5. Add credentials using "secret text" under the "Kind" options dropdown box
<img width="1202" alt="Screen Shot 2022-10-29 at 7 55 53 AM" src="https://user-images.githubusercontent.com/108026310/198830077-7ed5184c-2246-401f-a6ee-8bd8342b5a3a.png">
6. Input first set of Credentials using the following template: 
<img width="725" alt="Screen Shot 2022-10-29 at 8 04 23 AM" src="https://user-images.githubusercontent.com/108026310/198830379-eb9cb9dd-8b61-4097-a00c-d98beca8ed90.png">
7. Input second set of Credentials using the following template:
<img width="724" alt="Screen Shot 2022-10-29 at 8 07 24 AM" src="https://user-images.githubusercontent.com/108026310/198830520-c5ef9098-e0e6-4fbb-a93b-8b3fe765d63f.png">


## 4. Create a Pipeline build in Jenkins
1.  While logged into Jenkins, create a multibranch pipeline and connect it to your Github repo by adding Github credentials to Branch Sources
<img width="1120" alt="Screen Shot 2022-10-29 at 8 14 27 AM" src="https://user-images.githubusercontent.com/108026310/198830958-fddffb81-390d-40f9-9fdd-bfbdaf4ade59.png">
2. Build pipeline to run phases "build" "test" "init" "Plan" "Apply" (Destroy stage added succes through each stage


## Additions to the pipeline (Slack Notificiations)
1. Add Slack Notification plugin to Jenkins (Navigate to "manage jenkins" and then "Manage plugins" to search for slack plugin
2. Configure the Jenkins integration using: https://my.slack.com/services/new/jenkins-ci choose the channel of choice to receive notifications
3. Save the "Integration Token Credential ID" as it is needed to when creating credentials 
4. Configure system to add Slack workspace "kura-labs" and add secret text credential using the "Integration Token Credential ID" from the above step. User ID can be found in the slack profile (copy starting with "U" 

## Issues
1. ./terraform did not work to run and install Terraform even after modifying permissions
2. python3-venv needed
3. Needed to edit the Jenkinsfile for AWS Access Key information (updated AMI and key name)
4. Added AWS access key and ID
5. needed to remove ".pem" from key name
6. failed in the "Init" stage due to the following error:
<img width="957" alt="Screen Shot 2022-10-22 at 3 11 51 PM" src="https://user-images.githubusercontent.com/108026310/198831082-e843d92c-beea-438b-8265-c4fdfacfbb65.png">
This was due to inputing the Credentials incorrectly in the #3 phase "Configure credentials on Jenkins", step 6 and 7
