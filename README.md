# Jenkins container with Nginx container as proxy
[![Git](https://img.shields.io/badge/Git-1f425f.svg)]()
[![Docker](https://img.shields.io/badge/Docker-1f425f.svg)]()
[![Jenkins](https://img.shields.io/badge/Jenkins-1f425f.svg)]()

<br/>
Source: https://technology.riotgames.com/news/jenkins-ephemeral-docker-tutorial
<br/><br/><br/>

# jenkins.conf for default
proxy_pass         http://leeroy:8080;
<br/><br/>

# jenkins.conf for docker-compose
proxy_pass         http://jenkins_master_1:8080;
<br/><br/>

## Creating network container
```bash
$docker network create --driver  bridge jenkins-net
```

## Creating the persistent volumes
```bash
$docker volume create jenkins-log
$docker volume create jenkins-data
```

## Building custom images from dockerfile
```bash
$docker build -t jenkinsnginx jenkins-nginx/.
$docker build -t jenkinsmaster jenkins-master/.
```

## Running the containers in detached mode
Create and run each container in their respective port, sharing the same network so the traffic to jenkins has to pass through the nginx proxy, also mounting the persistent volumes

```bash
$docker run -p 80:80 --name=leeroyn --network jenkins-net -d jenkinsnginx
$docker run -p 8080:8080 -p 5000:5000 --name=leeroy --network jenkins-net --mount source=jenkins-log,target=/var/log/jenkins --mount source=jenkins-data,target=/var/jenkins_home -d jenkinsmaster
```

## Getting Jenkins first time password
Outputs the pass to terminal:
```bash
$docker exec leeroy cat /var/jenkins_home/secrets/initialAdminPassword
```

# Running with docker-compose

## Displaying logs from the hosted volume of a detached container
```bash
$docker exec <container_name> tail -f /var/log/jenkins/jenkins.log
```

## Building and run with composer

```bash
$docker-compose build
$docker-compose -p jenkins up -d
```

## Getting Jenkins first time password
Outputs the pass to terminal:
```bash
$docker exec leeroy cat /var/jenkins_home/secrets/initialAdminPassword
```

## Get first time pass
```bash
$docker exec jenkins_master_1 cat /var/jenkins_home/secrets/initialAdminPassword
```

## Delete everything
The "-v" flag also deletes the created volumes

```bash
$docker-compose -p jenkins down -v
```