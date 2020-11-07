# capstone
 udacity capstone project
 steps:
 Create ubuntu t3.micro
 ssh into newly created instance
 sudo apt-get update && sudo apt-get upgrade
 sudo apt-get install default-jdk 
 wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
sudo apt-get install awscli
install recommended plugins
install blue ocean plugins 
install aws credentials plugin

sudo apt-get install tidy
Re-use Jenkinsfile from project3
Configure Blue ocean pipeline 
Ensure pipeline works
Configure AWS credentials
configure docker in Jenkins
#Note add
#vi visudo
jenkins ALL=(ALL) NOPASSWD: ALL 
**to grant access to jenkins user
configure docker hub credentials
Push image to docker hub
==========================================================================
# https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html#install-eksctl-linux
# install eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version

# install kubectl
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.18.8/2020-09-18/bin/linux/amd64/kubectl
sudo chmod +x ./kubectl
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin
kubectl version --short --client

# Add user jenkins to group docker
sudo usermod -aG docker ubuntu
=========================================================================

Create Kuberentes cluster
Generate Kubeconfig file
Deploy image using rollout strategy (deployment.yml)
Check deployment
Purge system
