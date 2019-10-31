# Container, Docker and Kubernetes

## Docker Demo

### Demo 1 - Container

Container is just a running process ...

```shell
$ docker run ubuntu /bin/echo 'Hello world'
Hello world
```

Container will stay only if there is a running process ... you can 

start a monitoring process, like bash

```shell
docker run -t -i ubuntu /bin/bash
root@af8bae53bdd3:/#
```

or setup a loop

```shell
docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
docker logs -f <container-id>
```

TIP: stop and remove all containers in one command

for windows

```shell
FOR /f "tokens=*" %i IN ('docker ps -a -q') DO docker stop %i
FOR /f "tokens=*" %i IN ('docker ps -a -q') DO docker rm %i
```

for linux and Mac

```shell
docker stop $(docker ps -qa)
docker rm $(docker ps -qa)
```

### Demo 2 - About Docker Desktop for Windows

1) it's just a VM with remote docker access setup for you
2) it's for Windows 10 (client os only), not for Servers
3) Windows Containers and Linux Container -- all supported

### Demo 3 - Nginx with Web Access

```shell
docker run -itd -p 8080:80 nginx
docker run -itd -p 8081:80 nginx
```

### Demo 4 - Build ,Ship and Run

```shell
λ mkdir docker-training
λ cd docker-training\
λ mkdir php-webapp
λ cd php-webapp\
λ code .
```

In vscode, create file src/index.php and paste in the following code 

```php
<html>
<head>
    <title>Hello world!</title>
</head>
<body>
    <h3>My hostname is <?php echo getenv('HOSTNAME'); ?></h3>
</body>
</html>
```

create Dockerfile

```Dockerfile
FROM php:7.1-apache
COPY src/ /var/www/html/
```

Build

```shell
docker build -t php-webapp:v1 .
```

Ship

```shell
docker tag php-webapp:v1 idcfdemo.azurecr.io/php-webapp:v1
docker push idcfdemo.azurecr.io/php-webapp:v1
```

Run

```shell
ssh localadmin@vpn-lx.southeastasia.cloudapp.azure.com
```

## Kubernetes Demo

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: php-webapp
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: php-webapp
    spec:
      containers:
      - name: php-webapp
        image: idcfdemo.azurecr.io/php-webapp:v1
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: php-webapp
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: php-webapp
```

```shell
kubectl create -f kube-deploy.yaml
kubectl scale --replicas=5 ReplicationControllers/php-webapp
```
