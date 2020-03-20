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

# firewalld settings
$ sudo firewall-cmd --permanent --zone=public --add-service=http
$ sudo firewall-cmd --permanent --zone=public --add-service=https
$ sudo firewall-cmd --reload

# open port list (same 'netstat')
$ sudo ss -tulpn 

# nginx config
$ sudo vi /etc/nginx/nginx.conf

- http{} 안에 내용 추가

upstream nodejs_server{
    server localhost:3001 weight=10 max_fails=3 fail_timeout=10s;
    server localhost:3002 weight=10 max_fails=3 fail_timeout=10s;
}
server{
    listen          3333;
    server_name     localhost;

    location / {
            proxy_pass http://nodejs_server;
    }
}

# docker logs
http://localhost:3333/
http://localhost:3333/
http://localhost:3333/

$ docker logs -f server1
GET / 304 0.548 ms - -
GET /stylesheets/style.css 304 0.944 ms - -

$ docker logs -f server2
GET /stylesheets/style.css 304 0.944 ms - -
GET / 304 0.548 ms - -

