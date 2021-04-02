#!/bin/bash


# Initialize Ansible
echo "[TASK 1] Initialize Ansible & JDK"
apt-get install -y ansible sshpass python wget default-jdk maven >/dev/null 2>&1

# Install docker from Docker-ce repository
echo "[TASK 2] Install Docker Container Engine"
apt-get update
apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository \
   "deb https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
   $(lsb_release -cs) \
   stable"
apt-get update && apt-get install -y docker-ce=$(apt-cache madison docker-ce | grep 17.03 | head -1 | awk '{print $3}')


# Enable docker service
echo "[TASK 3] Enable and start docker service"
systemctl enable docker >/dev/null 2>&1
systemctl start docker

# Enable docker service
echo "[TASK 4] Enable and start docker-compse"
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

root@master:/vagrant# ls
bootstrap_master.sh  bootstrap.sh  bootstrap_worker.sh  ubuntu-xenial-16.04-cloudimg-console.log  Vagrantfile
root@master:/vagrant# cat  bootstrap_worker.sh
#!/bin/bash

# Getting Ready for DevOps & Ansible
echo "[TASK 1] Join node to Kubernetes Cluster"
apt-get update > /dev/null 2>&1
apt-get install -q -y sshpass python > /dev/null 2>&1


# Enable ssh password authentication
echo "[TASK 2] Enable ssh password authentication"
sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
sed -i 's/^PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
systemctl reload sshd
