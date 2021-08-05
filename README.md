# Solution

## Create infra vi aws cli

1. Create ec2 key pair

  `aws ec2 create-key-pair --key-name ec2-devops-key`

2. Add security group configurations to allow ssh to jenkins vm and rdp to windows vm and connection between jenkins and windows vm over winrm .

  ```bash
  $ aws ec2 create-security-group \
        --group-name ec2-jenkins-sg \
        --description "Jenkins security group"

  $ aws ec2 create-security-group \
        --group-name ec2-windows-sg \
        --description "Windows security group"

  $ aws ec2 authorize-security-group-ingress \
        --group-name ec2-windows-sg --protocol tcp \
        --port 3389 --cidr 0.0.0.0/0

  $ aws ec2 authorize-security-group-ingress \
        --group-name ec2-jenkins-sg --protocol tcp \
        --port 22 --cidr 0.0.0.0/0

  $ aws ec2 authorize-security-group-ingress \
        --group-name ec2--windows-sg --protocol tcp \
        --port 5985 --cidr 0.0.0.0/0

  $ aws ec2 authorize-security-group-ingress \
        --group-name ec2-windows-sg --protocol tcp \
        --port 5986 --cidr 0.0.0.0/0

  $ aws ec2 authorize-security-group-ingress \
        --group-name ec2-jenkins-sg --protocol tcp \
        --port 8080 --cidr 0.0.0.0/0
  ```

3. Launch windows server (user data will configure winrm)

  ```bash
  $ aws ec2 run-instances \
              --instance-type t2.small \
              --image-id ami-03295ec1641924349 \
              --user-data file://windows.txt  \
              --security-groups ec2-windows-sg \
              --key-name ec2-devops-key \
              --output text --query 'Instances[*].InstanceId'
  ```

4. Launch jenkins vm (user data will install jenkins and ansible in the server)

  ```bash
  $ aws ec2 run-instances \
              --instance-type t2.small \
              --image-id ami-0dc2d3e4c0f9ebd18 \
              --user-data file://jenkins.sh  \
              --security-groups ec2-jenkins-sg \
              --key-name ec2-devops-key \
              --output text --query 'Instances[*].InstanceId'
  ```

## Set up jenkins job and run

1. Access jenkins at port 8080 with jenkins vm IP.
2. Get admin password from /var/lib/jenkins/secrets/initialAdminPassword.
3. Login with admin password
4. Install ansible plugin
6. Create a new jenkins pipeline job with jenkinsfile from https://github.com/tejasri11/kaseya-devops-test.git
7. Execute the job by passing windows vm user, password and ip as build parameters.
