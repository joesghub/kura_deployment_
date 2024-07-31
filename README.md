
# Kura Deployment 1 - Retail Bank Application 

A Retail Bank wants to deploy an application to the cloud that is fully managed by AWS. In order to achieve this, their source code must be uploaded to and configured in AWS Elastic Beanstalk.

## Creating your EC2 Instance

### Key Pair
Setting up a key pair is an important step! 

> A key pair, consisting of a public key and a private key, is a set of security credentials that you use to prove your identity when connecting to an Amazon EC2 instance. For Linux instances, the private key allows you to securely SSH into your instance.[^1]

![Key Pair Diagram](https://docs.aws.amazon.com/images/AWSEC2/latest/UserGuide/images/ec2-key-pair.png)
[^1]: [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html?icmpid=docs_ec2_console)

### Security Groups

In creating our instance, we need to ensure only the proper entities can access our application and environments. 
1. The VPC that you’re launching your instance into must have an internet gateway attached to it. (Automatically setup)
2. The instance must be assigned a public IP address. (Auto-asigned)
3. We will add a security group rule to allow SSH from the IP address of the device that you’ll be logging in from (such as your work laptop). `(Port = 22, Source = 0.0.0/0)`
4. We add another security group rules to allow HTTP and HTTPS traffic from the internet. `(Port = 80, Source = 0.0.0.0/0)`
5. Finally, we add a security group for Jenkins to allow it to connect to our instance. `(Port = 8080, Source = 0.0.0/0)`

    **Hooray, we can securely launch our instance now!**

## Installing Jenkins

This Jenkins code snippet performs several tasks related to setting up a Jenkins server on a Debian-based system. Here's a breakdown of what each command does:

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


**Full Jenkins Installation Code:**
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

![Successful Jenkins Activation Message](https://github.com/joesghub/kura_deployment_1/blob/main/Successful%20Jenkins%20Installation%20and%20Start.png?raw=true)

**Congratulations on setting up your Jenkins pipeline! Now, if only we could see it?**

## Connecting to Jenkins by Web Browser


## Lessons Learned

What did you learn while building this project? What challenges did you face and how did you overcome them?

