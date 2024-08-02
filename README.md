
# Kura Deployment 1 - Retail Bank Application 

## Purpose
A Retail Bank wants to deploy an application to the cloud that is fully managed by AWS. In order to achieve this, their source code must be uploaded to and configured in AWS Elastic Beanstalk. This repository contains all the necessary files and steps to succefully setup the Retail Bank's web application.

**The workload will teach you the fundemantals of repository management, continuous integration, and continuous deployment!**

## Creating your EC2 Instance

### Key Pair
**Setting up a key pair is an important step!** 

> A key pair, consisting of a public key and a private key, is a set of security credentials that you use to prove your identity when connecting to an Amazon EC2 instance. For Linux instances, the private key allows you to securely SSH into your instance.[^1]

![Key Pair Diagram](https://github.com/joesghub/kura_deployment_1/blob/main/Screenshots/ec2-key-pair.png?raw=true)
[^1]: [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html?icmpid=docs_ec2_console)

### Security Groups

> [!SAFETY FIRST]  
>In creating our instance, we need to ensure only the proper entities can access our application and environments.

1. **The VPC that you’re launching your instance into must have an internet gateway attached to it. (Automatically setup)**
2. **The instance must be assigned a public IP address. (Auto-asigned)**
3. **We will add a security group rule to allow SSH from the IP address of the device that you’ll be logging in from (such as your work laptop). `(Port = 22, Source = 0.0.0/0)`**
4. **We add another security group rules to allow HTTP and HTTPS traffic from the internet. `(Port = 80, Source = 0.0.0.0/0)`**
5. **Finally, we add a security group for Jenkins to allow it to connect to our instance. `(Port = 8080, Source = 0.0.0/0)`**

        `HTTP (Hypertext Transfer Protocol)` and `SSH (Secure Shell)` are both network communication protocols that allow computers to share data and communicate. However, they have different purposes and security features:
    
        `HTTP` Used to transfer hypertext, such as web pages. A website that uses HTTP has "http://" in its URL.

        `SSH` Used to securely execute commands on a server and share data between computers. SSH uses public-key cryptography to encrypt data and ensure  it can't be intercepted or changed during transfer. SSH keys are considered more secure than passwords or authentication tokens. 

**Hooray, we can securely launch our instance now!**

## Installing Jenkins

> [!NOTE]  
>This Jenkins code snippet performs several tasks related to setting up a Jenkins server on a Debian-based system. Here's a breakdown of what each command does:

1. **Update and Install Packages:**
    ```bash
    sudo apt update && sudo apt install fontconfig openjdk-17-jre software-properties-common
    ```
    - `sudo apt update`: Updates the list of available packages and their versions.
    - `sudo apt install fontconfig openjdk-17-jre software-properties-common`: Installs the `fontconfig`, `openjdk-17-jre` (Java Runtime Environment), and `software-properties-common` packages.

2. **Add Python PPA and Install Python 3.7:**
    ```bash
    sudo add-apt-repository ppa:deadsnakes/ppa && sudo apt install python3.7 python3.7-venv
    ```
    - `sudo add-apt-repository ppa:deadsnakes/ppa`: Adds the deadsnakes PPA (Personal Package Archive), which contains newer versions of Python.
    - `sudo apt install python3.7 python3.7-venv`: Installs Python 3.7 and the Python 3.7 virtual environment package.

3. **Download Jenkins GPG Key:**
    ```bash
    sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    ```
    - `sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key`: Downloads the Jenkins GPG key and saves it to `/usr/share/keyrings/jenkins-keyring.asc`. This key is used to verify the authenticity of the Jenkins packages.

4. **Add Jenkins Repository and Update Package List:**
    ```bash
    echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
    ```
    - `echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null`: Adds the Jenkins repository to the list of sources from which APT will fetch packages. The `signed-by` option specifies the key to use for verifying the packages from this repository.

These steps collectively prepare the system to install Jenkins and ensure that the necessary dependencies (Java, Python 3.7, etc.) are in place.


### Full Jenkins Installation Code:
```bash
    $sudo apt update && sudo apt install fontconfig openjdk-17-jre software-properties-common && sudo add-apt-repository ppa:deadsnakes/ppa && sudo apt install python3.7 python3.7-venv
    $sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    $echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
    $sudo apt-get update
    $sudo apt-get install jenkins
    $sudo apt-get upgrade
    $sudo systemctl start jenkins
    $sudo systemctl status jenkins
```

![Successful Jenkins Activation Message](https://github.com/joesghub/kura_deployment_1/blob/main/Screenshots/Successful%20Jenkins%20Installation%20and%20Start.png?raw=true)

**Congratulations on setting up your Jenkins pipeline! Now, if only we could see it?**

## Connecting to Jenkins by Web Browser

*Remember we set up a security group for Jenkins to be able to access it?* 

**Well now that it is setup on our Ubuntu instance, we can access Jenkins through our web browser!**

![Connecting to Jenkins through your Instance](https://github.com/joesghub/kura_deployment_1/blob/main/Screenshots/Connecting%20to%20Jenkins%20through%20your%20Instance.png?raw=true)

### Steps to Connect to Jenkins through Webview

1. **Copy the Public IPv4 address (Green Box 1) and attach the Jenkins port number to access the webpage `0.0.0.0:8080`**

2. **Unlock Jenkins with the password stored in `/var/lib/jenkins/secrets/initialAdminPassword`**

3. **Use `sudo cat /var/lib/jenkins/secrets/initialAdminPassword` in your terminal and enter the pasword into the Jenkins Sign-In page.**

4. **Select "Install suggested plugins" and create a first admin user.**

### Steps to Setup a Multi-Branch Pipeline

1. **Click on “New Item” in the menu on the left of the page and Enter a name for your pipeline.**

2. **Select “Multibranch Pipeline”, name your pipeline, and select “Add source” under “Branch Sources” for “GitHub”** 

3. **Click “+ Add” and select “Jenkins”. In the resulting window, make sure “Kind” reads “Username and password”** 

4. **Under “Username” enter your GitHub username and under “Password” enter your GitHub personal access token.**

5. **Select your Github credentials, enter the repository HTTPS URL, and click "Validate"**

6. **Enter the Private IP address (Blue Box 2) of your instance as the Jenkins Configuration Url with the port `:8080`.**

![Jenkins Url Configuration](https://github.com/joesghub/kura_deployment_1/blob/main/Screenshots/Jenkins%20Url%20Configuration.png?raw=true)


****You're heating up!! Now let's set our sights on this so-called Beanstalk?!****

![Jenkins Scan Repository Log](https://github.com/joesghub/kura_deployment_1/blob/main/Screenshots/Jenkins%20Scan%20Repository%20Log.png?raw=true)

## Deploying our Application on AWS Elastic Beanstalk


### IAM Security Roles 

> [!SAFETY FIRST]  
> In AWS Elastic Beanstalk, we will be using **IAM (Identity and Access Management) roles**.

> An IAM role is an IAM identity that you can create in your account that has specific permissions. An IAM role is similar to an IAM user, in that it is an AWS identity with permission policies that determine what the identity can and cannot do in AWS.[^2]

[^2]: [AWS IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)

1. **Navigate to your IAM Dashboard and select "Roles" in the "Access management" dropdown on your left panel.**

2. **Click "Create Role" and select "AWS service" as the `Trusted Entity Type`.** 

3. **Use the dropdown to choose "Elastic Beanstalk" as the `Use cases for other AWS Services` and select the "Customizabble" option.** 

4. **Click "Next" two times so you arrive at `Step 3: Name, review, and create`. Enter "aws-elasticbeanstalk-service-role" in the `Role name` and click "Create Role"**

5. **Click "Create Role" and select "AWS service" as the `Trusted Entity Type`.** 

6. **Select `EC2` ad the `Use Case`. Click next to arrive at `Step2: Add permissions`.** 

7. **Search and select: `AWSElasticBeanstalkMulticontainerDocker`, `AWSElasticBeanstalkWebTier`, & `AWSElasticBeanstalkWorkerTier`**

8. **Click "Next" one time so you arrive at `Step 3: Name, review, and create`. Enter "Elastic-EC2" in the `Role name` and click "Create Role"**

9. **Congratulations! You've set up your two IAM roles!**

### AWS Elastic Beastalk Setup

**What is AWS Elastic Beanstalk?** 
>With Elastic Beanstalk, you can quickly deploy and manage applications in the AWS Cloud without having to learn about the infrastructure that runs those applications. Elastic Beanstalk reduces management complexity without restricting choice or control. You simply upload your application, and Elastic Beanstalk automatically handles the details of capacity provisioning, load balancing, scaling, and application health monitoring.[^3]

![Beanstalk System Design](https://github.com/joesghub/kura_deployment_1/blob/main/Screenshots/aws%20beanstalk%20sys%20diagram.png?raw=true)

[^3]: [AWS Elastic Beanstalk Docs](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html)


1. **Navigate to the AWS Elastic Beanstalk console page and select "Create Environment" on the "Environment" page in your left panel.**

2. **Select "Web server environment" as the `Environment Tier` and enter an Application name.** 

3. **Choose "Python 3.7" as the "Managed platform". In `Application Code` choose "Upload your code" and upload your zipped application files with the "local file" option. ** 

> [!NOTE]  
>When you create a ZIP file in Mac OS X Finder or Windows Explorer, make sure you zip the files and subfolders themselves, rather than zipping the parent folder.
1. Open your top-level project folder and select all the files and subfolders within it. Do not select the top-level folder itself.
2. Right-click the selected files, and then choose Compress X items, where X is the number of files and subfolders you've selected. [^4]

[^4]: [Create an application source bundle](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/applications-sourcebundle.html)

4. **For `Presets` select "Single instance (free tier eligible)" and then click "Next". Select the "aws-elasticbeanstalk-service-role" for the `Service role` and "EC2 profile" for the `EC2 instance profile`, then click "Next".**

5. **Select the default `VPC` and `Instance Subnet` as "us-east-1a" and then click "Next".** 

6. **Select "General Purpose (SSD) for `Root volume type` and assign it 10 GB. Under `Environment type` in the `Auto scaling group` of `Capacity` ensure that "Single instance" is selected.** 

7. **Select "General Purpose (SSD) for `Root volume type` and assign it 10 GB. Under `Environment type` in the `Auto scaling group` of `Capacity` ensure that "Single instance" is selected. lower down, also check that `Instance types` is ONLY "t3.micro" (remove all others if present), then click "Next"

8. **Select 'BASIC' health reporting under the monitoring section. NOT "ENHANCED". De-select "Activated" under `Managed Updates` if selected. Continue to the "Review" page and then click "Submit".**

![Successful Beanstalk Setup](https://github.com/joesghub/kura_deployment_1/blob/main/Screenshots/EBS%20Deployment.png?raw=true)

**I think we did it...right? What if we click on this `Domain` link in our `Environment overview`**

## Welcome to Retail Banking

**We did it Joe!**

![Successful Beanstalk Setup](https://github.com/joesghub/kura_deployment_1/blob/main/Screenshots/Retail%20Banking%20App%20Deployed.png?raw=true)

## System Design

![Retail banking App System Design ](https://github.com/joesghub/kura_deployment_1/blob/main/Screenshots/Retail%20Banking%20Application%20System%20Design.drawio.png?raw=true)

## Issues, Troubleshooting, and Lessons Learned 

I was able to create my Jenkins pipeline, run a successful test, and create my Beanstalk environment; however, I ran into a `502 Bad Gateway nginx error`!

![502 Bad Gateway nginx error](https://github.com/joesghub/kura_deployment_1/blob/main/Screenshots/502%20Bad%20Gateway.png?raw=true)

At first, I misunderstood the relationship between EC2, Jenkins, Github, and Beanstalk. Believeing they were more connected than they are, I sought out the issue in the nginx files. After editing too many system files, I was stumped and even further away from the actual issue. I set up some time to meet with an Instructor which proved invaluable.

My first step towards the solution was identifying the true nature of my system's design. While I thought Jenkins was responsible for uploading updated versions of the repository to Beanstalk, in reality it was me! 

Jenkins does test and integrate new versions of code added to the paired repository. Elastic Beanstalk does automatically deploy new versions of the repository you add. With this improve dunderstanding I decided to focus on Elastic Beanstalk as Jenkins ran successful tests on my repository. 

My environment had `Green` health and persisted over multiple days, so I honed further in on what code was being deployed. When I added my local file to EBS, I uploaded the `.zip` download from my Github repository. That `.zip` file was the *Parent Folder*, containing all the files, zipped. 

![Selecting Files in a Parent Folder](https://github.com/joesghub/kura_deployment_1/blob/main/Screenshots/Selecting%20Files%20in%20a%20Parent%20Folder.png?raw=true)

Our instructor shared a tip about "Application Source Bundles" that explained the proper method to zip the application code files. After I zipped the all the application code files within the *Parent Folder*, I was able to re-deploy my web application and access the Retail Banking App![^4]

![Compressing Files into a Source Bundle Zip](https://github.com/joesghub/kura_deployment_1/blob/main/Screenshots/Compressing%20Files%20into%20a%20Source%20Bundle%20Zip.png?raw=true)


## Optimization

1. **What are the benefits of using managed services for cloud infrastructure?** 
- Not needing to worry about the maintenance that goes into scaling and monitoring your applications. 
- Receiving alerts about application performance.
- Reliable scaling within the cloud to capture business opportunity during traffic spikes.
- Redundancy for our application that new versions will be launched if failures occur. 
- Ability to save costs and spin down usage during traffic lulls. 

2. **What are some issues that a retail bank would face choosing this method of deployment and how would you address/resolve them?** 
- The main issue with this method is it does not work how I assumed it did. Any newly tested code repositories from Jenkins would need to be manually downloaded and re-uploaded to EBS. At the Retail Bank it would require at least one person to wait for Jenkins to notify them of recently tested code. Then that person would need to download and properly zip the Source Bundle for EBS to re-deploy. One thumb down :(

3. **What are other disadvantages of using elastic beanstalk or similar managed services for deploying applications?**
-  I would need to transfer logs and database information to my chosen service provider or use their network of services to derive insights from the user app behavior. 


## Conclusion 

I mentioned to a fellow DevOps Engineer that I was learning Jenkins while completing this workload. They sighed, chuckled, and offered any help possible, but what they said after was possibly the most helpful thing they could have said:

> Sometimes you are working on a VM and are unable to download newer applications for security or integrity purposes. It helps to know what tools engineers used before when encountering legacy systems, as they can not always be sudo'd and changed. 
