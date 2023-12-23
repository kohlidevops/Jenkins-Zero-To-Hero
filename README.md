# What is Jenkins?

1. Jenkins is a free tool that helps software developers work faster and more efficiently. It takes care of repetitive tasks like building, testing, and delivering code.
2. It keeps an eye on code changes and automatically starts the necessary processes.
3.It supports a wide range of plugins, allowing seamless integration with various tools and technologies
4. By using Jenkins, developers can save time, work together smoothly, and make sure their software works correctly before releasing it to users.

## AWS EC2 Instance

- Go to AWS Console
- Instances(running)
- Launch instances

<img width="994" alt="Screenshot 2023-02-01 at 12 37 45 PM" src="https://user-images.githubusercontent.com/43399466/215974891-196abfe9-ace0-407b-abd2-adcffe218e3f.png">

### Install Jenkins.

Pre-Requisites:
 - Java (JDK)

### Run the below commands to install Java and Jenkins

Install Java

```
sudo apt update
sudo apt install openjdk-11-jre
```

Verify Java is Installed

```
java -version
```

Now, you can proceed with installing Jenkins

```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

**Note: ** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as show below.

- EC2 > Instances > Click on <Instance-ID>
- In the bottom tabs -> Click on Security
- Security groups
- Add inbound traffic rules as shown in the image (you can just allow TCP 8080 as well, in my case, I allowed `All traffic`).

<img width="1187" alt="Screenshot 2023-02-01 at 12 42 01 PM" src="https://user-images.githubusercontent.com/43399466/215975712-2fc569cb-9d76-49b4-9345-d8b62187aa22.png">


### Login to Jenkins using the below URL:

http://<ec2-instance-public-ip-address>:8080    [You can get the ec2-instance-public-ip-address from your AWS EC2 console page]

Note: If you are not interested in allowing `All Traffic` to your EC2 instance
      1. Delete the inbound traffic rule for your instance
      2. Edit the inbound traffic rule to only allow custom TCP port `8080`
  
After you login to Jenkins, 
      - Run the command to copy the Jenkins Admin Password - `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
      - Enter the Administrator password
      
<img width="1291" alt="Screenshot 2023-02-01 at 10 56 25 AM" src="https://user-images.githubusercontent.com/43399466/215959008-3ebca431-1f14-4d81-9f12-6bb232bfbee3.png">

### Click on Install suggested plugins

<img width="1291" alt="Screenshot 2023-02-01 at 10 58 40 AM" src="https://user-images.githubusercontent.com/43399466/215959294-047eadef-7e64-4795-bd3b-b1efb0375988.png">

Wait for the Jenkins to Install suggested plugins

<img width="1291" alt="Screenshot 2023-02-01 at 10 59 31 AM" src="https://user-images.githubusercontent.com/43399466/215959398-344b5721-28ec-47a5-8908-b698e435608d.png">

Create First Admin User or Skip the step [If you want to use this Jenkins instance for future use-cases as well, better to create admin user]

<img width="990" alt="Screenshot 2023-02-01 at 11 02 09 AM" src="https://user-images.githubusercontent.com/43399466/215959757-403246c8-e739-4103-9265-6bdab418013e.png">

Jenkins Installation is Successful. You can now starting using the Jenkins 

<img width="990" alt="Screenshot 2023-02-01 at 11 14 13 AM" src="https://user-images.githubusercontent.com/43399466/215961440-3f13f82b-61a2-4117-88bc-0da265a67fa7.png">

## Jenkins Master Slave Architecture

1. Jenkins master-slave architecture is a way to distribute workloads in Jenkins.
2. In simple terms, it’s like having a boss (master node) and assistants (slave nodes) in a company.
3. The boss manages and coordinates tasks, while the assistants handle the actual work.

### Benefits

1. It improves performance and scalability.
2. The workload is divided among multiple machines, allowing tasks to be executed simultaneously.
3. This speeds up the overall process and enables handling larger workloads.
4. If the master node fails, the slave nodes can continue working independently. This ensures that the development and deployment processes are not disrupted.
5. Different slave nodes can be configured with specific software, hardware, or operating systems to handle specific tasks.

## Jenkins Architecture - Difference between Worker node as slave and docker container as slave

![image](https://github.com/kohlidevops/Jenkins-Zero-To-Hero/assets/100069489/9029dab5-55b2-4bbb-9aad-3ceabbf56f6a)

### Permanent Slaves

1. A permanent slave in Jenkins master-slave architecture is a dedicated machine that is always available to perform tasks assigned by the master node.
2. Permanent slaves have fixed resources and are useful for consistent workloads or when specific hardware or software configurations are required.
3. They are just like VM running on your HostOS, which consumes a lot of physical resources.

### Dynamic Slaves

1. Dynamic slaves are more like on-demand assistants.
2. They are created when needed and released when the work is done.
3. Dynamic slaves can be provisioned on different machines, including virtual machines or containers, depending on the workload.
4. They provide flexibility and adaptability to handle varying workloads.

### Challenges with Worker node as Slave

1. Need multiple machine act as a worker nodes
2. If there is no requirements for any worker node, then it would be idle
3. Not handling multiple versions in single machine - nodejs 8,9,12
4. Cost would be painful

### Advantages of using docker container as Slave

1. When there are no pending tasks, the resources are freed up, allowing other jobs or tasks to utilize them.
2. Dynamic slaves enable scaling. If the workload increases, more slaves can be provisioned to handle the additional tasks.
3. Once the workload decreases, the extra slaves can be terminated, saving resources and costs.
4. Dynamic slaves support parallel execution. Multiple tasks can run concurrently on different slave nodes, significantly reducing the overall execution time
   
## Install the Docker Pipeline plugin in Jenkins:

   - Log in to Jenkins.
   - Go to Manage Jenkins > Manage Plugins.
   - In the Available tab, search for "Docker Pipeline".
   - Select the plugin and click the Install button.
   - Restart Jenkins after the plugin is installed.
   
<img width="1392" alt="Screenshot 2023-02-01 at 12 17 02 PM" src="https://user-images.githubusercontent.com/43399466/215973898-7c366525-15db-4876-bd71-49522ecb267d.png">

Wait for the Jenkins to be restarted.


## Docker Slave Configuration

Run the below command to Install Docker

```
sudo apt update
sudo apt install docker.io
```
 
### Grant Jenkins user and Ubuntu user permission to docker deamon.

```
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```

Once you are done with the above steps, it is better to restart Jenkins.

```
http://<ec2-instance-public-ip>:8080/restart
```

The docker agent configuration is now successful.




