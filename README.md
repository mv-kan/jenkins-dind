# jenkins-dind
https://github.com/devopsjourney1/jenkins-101
Create docker host for jenkins 

```
docker run \
  --name jenkins-docker \
  --restart=on-failure \
  --detach \
  --privileged \
  --network jenkins \
  --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  docker:dind \
  --storage-driver overlay2
```

Find out ip address of docker host 
```
docker inspect jenkins-docker | grep IPAddress
# this is the IP address you should add to docker configuration in Jenkins 
```

Then build jenkins 
```
docker build -t myjenkins-blueocean:2.452.1-1 -f DockerJenkins.Dockerfile .
```

And run builded jenkins 
```
docker run \
  --name jenkins-blueocean \
  --restart=on-failure \
  --detach \
  --network jenkins \
  --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client \
  --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 \
  --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  myjenkins-blueocean:2.452.1-1
```
