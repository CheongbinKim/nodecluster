# nodecluster

# install docker
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
$ sudo yum install docker-ce docker-ce-cli containerd.io
$ sudo usermod -aG docker kev1n
$ systemctl enable docker
$ reboot

# epel
$ sudo yum install epel-release

# nodejs
$ sudo yum install nodejs 
$ sudo npm install express-generator -g

# testApp
$ express --view=ejs clusterapp
$ cd clusterapp
$ npm install

$ node -v
v6.17.1

$ vi dockerfile
FROM node:6.17.1
MAINTAINER Kev1n kev1n.tistory.com

RUN mkdir -p /app
WORKDIR ./app
ADD ./ ./app

# Build
$ docker build --tag node_cluster:0.0.1 .

# Create
$ docker create --name NODE_CLUSTER_SERVER -p 3000:3000 node_cluster:0.0.1
$ docker ps -a

# Start
$ docker start NODE_CLUSTER_SERVER
$ docker ps

$ docker build --tag node_cluster:0.0.2 .

# 2core system
$ docker create --name server1 -p 3001:3000 node_cluster:0.0.2
$ docker create --name server2 -p 3002:3000 node_cluster:0.0.2
$ docker start server1
$ docker start server2

# nginx load balancing
$ sudo yum install nginx

# selinux port open
$ sudo semanage port -a -t http_port_t -p tcp 8081




