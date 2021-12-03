
yum update -y


yum install ansible -y
yum install nginx -y
yum install git -y


curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
. ~/.nvm/nvm.sh

nvm install node

mkdir server && cd server

npm install express




var express = require('express');
var app = express();
const util = require('util');
const exec = util.promisify(require('child_process').exec);

app.post('/', function(req, res){
    try {
        console.log('executing deployment...');
        exec("ansible-pull -U git@github.com:<GitHubUser>/<repo-name>.git <playbook>.yml", (error, stdout, stderr) => {
                if (error) {
                console.log(`error: ${error.message}`);
                return;
        }
        if (stderr) {
                console.log(`stderr: ${stderr}`);
                return;
        }

        console.log(`stdout: ${stdout}`);

        });
    } catch (e) {
        console.log(e);
    }

    res.json({ received: true });
});

app.listen(8080, '127.0.0.1');


exec("ansible-pull -U git@github.com:<GitHubUser>/<repo-name>.git <playbook>.yml")


node app.js

ssh-keygen -t rsa -b 4096 -C <your_email@example.com>

eval "$(ssh-agent -s)"

Agent pid 1111



server {
    listen       80 default_server;
    listen       [::]:80 default_server;
    server_name _;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    error_page 404 /404.html;
        location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    }
}


systemctl start nginx 

systemctl enable nginx


ssh-rsa <your_email@example.com>


ALTERNATIVE METHOD - 



AWSTemplateFormatVersion: 2010-09-09
Parameters:
  SubnetID:
    Type: AWS::EC2::Subnet::Id
    Description: Subnet to deploy EC2 instance into
  SecurityGroupIDs:
    Type: List<AWS::EC2::SecurityGroup::Id>
    Description: List of Security Groups to add to EC2 instance
  KeyName:
    Type: AWS::EC2::KeyPair::KeyName
    Description: >-
      Name of an existing EC2 KeyPair to enable SSH access to the instance
  InstanceType:
    Description: EC2 instance type
    Type: String
    Default: t2.micro
Mappings:
  AWSRegionToAMI:
    us-east-1:
      AMIID: ami-0a887e401f7654935
Resources:
  EC2Instance:
    Type: AWS::EC2::Instance 
    Properties:
      ImageId:
        !FindInMap 
          - AWSRegionToAMI 
          - !Ref AWS::Region
          - AMIID
      InstanceType: !Ref InstanceType
      KeyName: !Ref KeyName
      SecurityGroupIds: !Ref SecurityGroupIDs
      SubnetId: !Ref SubnetID
      UserData:
        Fn::Base64: 
          !Sub |
              #!/bin/bash -ex
              amazon-linux-extras install epel
              yum update -y
              yum install ansible -y
              yum install nginx -y
              yum install git -y
              systemctl start nginx
              systemctl enable nginx
              curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
              export NVM_DIR="$HOME/.nvm"
              [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
              nvm install node
              cat <<EOF >> /home/ec2-user/.bashrc
              export NVM_DIR="/.nvm"
              [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
              EOF
              cd /home/ec2-user
              mkdir server && cd server
              npm install express
      Tags: 
        -
          Key: Name
          Value: Ansible - CloudFormation    
        -
          Key: Environment
          Value: Development

