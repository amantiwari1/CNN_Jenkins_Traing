# CNN_Jenkins_Traing

first lets make create dir and after change directory web then create directory 'website' and create file 'Dockerfile'

## Create directory and File
```
$mkdir web

$cd web 

$mkdir website

$touch Dockerfile
```

Create Simple index.html in Website Folder

## Create Dockerfile

now,
need write code in dockerfile

should be likes this 


```
$vim Dockerfile
```
---



```
FROM centos:7

RUN yum install python36 -y
RUN pip3 install keras tensorflow

RUN yum install wget -y
RUN wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
RUN rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
RUN yum install git -y

WORKDIR /home/Keras

COPY /home/aman/Keras/new_imges/* /home/Keras/

RUN echo -e "jenkins ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

CMD ["java", "-jar", "/usr/lib/jenkins/jenkins.war"]
````

## Build Image using Dockerfile

lets make build image using dockerfile with web service (httpd)

```
$docker build -t CNN_Keras .
```
note: dot means current directory




# Now we Create job in Jenkins

## JOB 1


create new job name is Job1 

![createjob](images/ShooterScreenshot-32-12-05-20.png)

copy and paste from github url : https://github.com/amantiwari1/keras_model_training.git

go to Source Code Management and tick git

![git](images/ShooterScreenshot-34-13-05-20.png)

now tick in GitHub hook trigger for GITScm polling

![git](images/ShooterScreenshot-35-13-05-20.png)

create some shell in copy file

![git](images/ShooterScreenshot-36-13-05-20.png)


## JOB 2


now another job to start docker httpd images
and copy it and run it server with port 
```
if docker ps -a | grep test_server
then 
docker rm -f test_server
docker run -dit --name test_server -p 8081:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd
else 
docker run -dit --name test_server -p 8081:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd
fi
```

![git](images/ShooterScreenshot-39-13-05-20.png)

## JOB 3
Job 3 is to test our application

```
export status=$(curl -s -t -w "%(http_code)" -o /dev/null 172.17.05:8081)

if [$status -eq 200] 
then exit 1
else
exit 0
fi

```

![git](images/ShooterScreenshot-38-13-05-20.png)

## JOB 4


if it is working in the test (joB3) then 

it should run in shell like this 

```
systemctl restart httpd
```
![git](images/ShooterScreenshot-40-13-05-20.png)


now i am going to build some pipelines
