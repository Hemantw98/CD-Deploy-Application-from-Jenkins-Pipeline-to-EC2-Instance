## CD-Deploy-Application-from-Jenkins-Pipeline-to-EC2-Instance

### Technologies used:

AWS, Jenkins, Docker, Linux, Git, Java, Maven, Docker Hub

### Project Description:

Prepare AWS EC2 Instance for deployment (Install Docker) Create ssh key credentials for EC2 server on Jenkins

Extend the previous CI pipeline with deploy step to ssh into the remote EC2 instance and deploy newly built image from Jenkins server

Configure security group on EC2 Instance to allow access to our web application

### Here are the step-by-step instructions for the project:

### 1. Prepare AWS EC2 instance:

Log in to the AWS console and create a new EC2 instance.

Choose an appropriate Amazon Machine Image (AMI) for your application.

Choose the instance type based on the requirements of your application.

In the Security Group section, allow incoming traffic on port 22 (for SSH access) and port 80 (for web traffic).

Launch the instance and note the public IP address.

### 2. Install Docker on the EC2 instance:

 Connect to the EC2 instance using SSH and run the following commands:
 
        sudo yum update -y
    sudo yum install -y docker
    sudo service docker start
    sudo usermod -a -G docker ec2-user
    
The above commands will update the packages, install Docker, start the Docker service, and add the current user to the docker group to allow running Docker commands without sudo.

### 3. Create SSH key credentials for the EC2 server on Jenkins:

Open the Jenkins dashboard and navigate to "Credentials".

Click on "System" and then "Global credentials".

Click on "Add Credentials" and choose "SSH Username with private key".

Enter a suitable ID and description for the credentials.

Paste the private key of the EC2 instance in the "Private Key" field.

Save the credentials.

### 4. Extend the previous CI pipeline with deploy step to ssh into the remote EC2 instance and deploy the newly built image from Jenkins server:

Update your Jenkins pipeline script to include a deploy stage.

The deploy stage should use the credentials created in step 3 to SSH into the EC2 instance and run Docker commands to deploy the newly built Docker image.

Here's an example script for the deploy stage:

    stage('Deploy') {
       steps {
           sshagent(['EC2_ssh_key']) {
               sh "ssh -o StrictHostKeyChecking=no ec2-user@<EC2_INSTANCE_IP> 'docker pull <DOCKER_HUB_USERNAME>/<DOCKER_IMAGE>:<VERSION>'"
               sh "ssh -o StrictHostKeyChecking=no ec2-user@<EC2_INSTANCE_IP> 'docker stop <CONTAINER_NAME> || true'"
               sh "ssh -o StrictHostKeyChecking=no ec2-user@<EC2_INSTANCE_IP> 'docker rm <CONTAINER_NAME> || true'"
               sh "ssh -o StrictHostKeyChecking=no ec2-user@<EC2_INSTANCE_IP> 'docker run -d --name <CONTAINER_NAME> -p 80:8080 <DOCKER_HUB_USERNAME>/<DOCKER_IMAGE>:<VERSION>'"
           }
       }
    }

Note that you need to replace the placeholders <EC2_INSTANCE_IP>, <DOCKER_HUB_USERNAME>, <DOCKER_IMAGE>, <VERSION>, and <CONTAINER_NAME> with the appropriate values for your application.

### 5. Configure security group on EC2 Instance to allow access to our web application:

In the AWS console, navigate to the Security Groups section and select the security group associated with your EC2 instance.

Add an inbound rule to allow incoming traffic on port 80 from any source.

Save the changes.

That's it! Now, whenever you trigger a build in Jenkins, the pipeline will build your Java application, package it in a Docker container, push the image to Docker Hub, and deploy it to the EC2 instance automatically.

          


    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
