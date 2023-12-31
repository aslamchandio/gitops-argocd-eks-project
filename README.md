# GitOPS ArgoCD EKS Project

## What this does?
This repo along with https://github.com/aslamchandio/kubernetesmanifest.git creates a Jenkins pipeline with GitOps to deploy code into a Kubernetes cluster. CI part is done via Jenkins and CD part via ArgoCD (GitOps).

## Jenkins installation on Ubuntu 22.3-LTS

### References
- https://www.jenkins.io/doc/tutorials/tutorial-for-installing-jenkins-on-AWS/
- https://www.jenkins.io/doc/book/installing/linux/#debianubuntu

### Step-01: 
- Update Ubuntu
```
sudo apt update -y
sudo apt upgrade -y
sudo apt autoclean -y
sudo apt install zip unzip git nano vim wget net-tools vim nano htop tree  -y

```
### Step-02:
- DEFAULT RULES
```

sudo ufw status verbose

sudo ufw default deny incoming
sudo ufw default allow outgoing


sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw allow 8080

sudo ufw enable
sudo ufw status verbose

```

### Step-03:
- Update & Install Java JDK
```

sudo apt update
sudo apt install fontconfig openjdk-17-jre
java -version

```

### Step-04:
- Install Jenkins (Long Term Support release)
- A LTS (Long-Term Support) release is chosen every 12 weeks from the stream of regular releases as the stable release for that time period. It can be installed from the debian-stable apt repository.
```

sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update 

sudo apt install Jenkins

sudo systemctl status jenkins

sudo systemctl enable Jenkins --now

ps -aux | grep jenkins

netstat -planet | grep 8080

```

### Step-05:
- Change Port of Jenkins 8080 to 8181:(if 8080 Port already runnning in your Network)
```

sudo systemctl edit jenkins --full

Environment="JENKINS_PORT=8080"   to    Environment="JENKINS_PORT=8181"

sudo systemctl restart jenkins

systemctl status jenkins

- change port in jenkins UI

Dashboard > Manage Jenkins > System >  

Jenkins Location > Jenkins URL > http://18.77.11.12:8080/  to   http://18.77.11.12:8181/

```

- Jenkins plugins:
```

- Install the following plugins for the demo.

Amazon EC2 plugin (No need to set up Configure Cloud after)
Docker plugin
Docker Pipeline
GitHub Integration Plugin
Parameterized trigger Plugin

```

## Docker installation on Ubuntu 22.3-LTS

### Step-01: 
- Add Docker's official GPG key
```

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

```

### Step-02: 
- Add the repository to Apt sources 
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

  sudo apt-get update

```

### Step-03: 
- Install Docker
```

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

- Enable Docker Service

systemctl status docker
sudo systemctl enable docker --now

sudo usermod -aG docker ubuntu

- Run `sudo chmod 666 /var/run/docker.sock` on the VM after Docker is installed.

```

## ArgoCD installation 

Install ArgoCD in your Kubernetes cluster following this link - https://argo-cd.readthedocs.io/en/stable/getting_started/

### Requirements

- Installed kubectl command-line tool.
- Have a kubeconfig file (default location is ~/.kube/config).
  CoreDNS. Can be enabled for microk8s by microk8s enable dns && microk8s stop && microk8s start
 
### Step-01:
- Install Argo CD on K8S
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

This will create a new namespace, argocd, where Argo CD services and application resources will live.

```

### Access The Argo CD API Server
- Service Type Load Balancer
```

Change the argocd-server service type to LoadBalancer:

kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

```

- Port Forwarding:
```

Kubectl port-forwarding can also be used to connect to the API server without exposing the service.

kubectl port-forward svc/argocd-server -n argocd 8080:443

```

### Step-02:
- ArgoCD install on Linux
```

curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64

argocd admin initial-password -n argocd

This password must be only used for first time login. We strongly recommend you update the password using `argocd account update-password`.

argocd admin initial-password -n argocd

















Jenkins is installed on EC2. Follow the instructions on https://www.jenkins.io/doc/tutorials/tutorial-for-installing-jenkins-on-AWS/ . You can skip "Configure a Cloud" part for this demo. Please note some commands from this link might give errors, below are the workarounds:

1. If you get daemonize error while running the command `sudo yum install jenkins java-1.8.0-openjdk-devel -y` then , run the commands from the answer of https://stackoverflow.com/questions/68806741/how-to-fix-yum-update-of-jenkins

2. Install Docker on the EC2 after Jenkins is installed by following the instructions on https://serverfault.com/questions/836198/how-to-install-docker-on-aws-ec2-instance-with-ami-ce-ee-update

3. Run `sudo chmod 666 /var/run/docker.sock` on the EC2 after Docker is installed.

4. Install Git on the EC2 by running `sudo yum install git`

### Jenkins plugins

Install the following plugins for the demo.
- Amazon EC2 plugin (No need to set up Configure Cloud after)
- Docker plugin  
- Docker Pipeline
- GitHub Integration Plugin
- Parameterized trigger Plugin

## ArgoCD installation 

Install ArgoCD in your Kubernetes cluster following this link - https://argo-cd.readthedocs.io/en/stable/getting_started/

## How to run!
Follow along with my Udemy Kubernetes course lectures (GitOps Chapter) to understand how it works, detailed setup instructions, with step by step demo. My highest rated Kubernetes EKS discounted Udemy course link in www.cloudwithraj.com