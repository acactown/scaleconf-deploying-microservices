![scaleconf-deploying-microservices](dockermachine.png)

# Expose container in cloud using docker-machine

`docker-machine` is a docker tool (docker client) and is one of the docker mechanism to deploy containers to cloud providers

`docker-machine` uses "drivers https://docs.docker.com/machine/drivers/gce/" to deploy virtual instances in popular cloud providers. Every driver depends on a few configurations to work.

Some useful commands from docker-machine client

obtaining a list of currently docker machines
```sh
$ docker-machine ls
```
obtaining which docker machine is being used 
```sh
$ docker-machine active
```
Gets useful information from docker-machine to access the docker machine  
```sh
$ docker-machine env <docker-machine-name>
```
Creates a docker-machine inside specific environments depending on the driver (google cloud, azure, aws, virtual box, etc)  
```sh
$ docker-machine create -d <driver>
```

## Azure driver

```sh
$ docker-machine create -d azure --azure-ssh-user ops --azure-subscription-id <SubscriptionId> --azure-open-port 80 azuremachine
```
You will be asked to login with your account Azure account. The process takes about 5 minutes.

Connect to docker machine

```sh
$ docker-machine ls
$ docker-machine env <machine name>
$ docker-machine active 
```
Let´s deploy our site

```sh
$ docker build -t microservice .
$ docker run -d -p 80:80 microservice
```
Verify public ip and test again. 

## Amazon EC2 driver
To create an EC2 instance in AWS running Docker that we will later command from our local computer, we need the following variables set up, or you can use the last command: 

```
export AWS_ACCESS_KEY_ID=<your aws iam key>
export AWS_SECRET_ACCESS_KEY=<your aws iam key>

# According to when your AWS account was created, you may also need the following
export AWS_VPC_ID=vpc-xxxxxxxxx
export AWS_SUBNET_ID=subnet-xxxxxxxxx
```

```sh
$ docker-machine create -d amazonec2 awsmachine
or
$ docker-machine create --driver amazonec2 --amazonec2-access-key AKI******* --amazonec2-secret-key 8T93C*******  awsmachine
```

Now you can deploy containers inside an Amazon´s virtual machine

```sh
$ docker build -t microservice .
$ docker run -d -p 80:80 microservice
```
Verify public ip, create and inbound rule for HTTP and test with the public ip. 


## Google Cloud driver

To create a compute instance(VM) in google cloud, we need to follow the `gcloud` tools [gcloud install process](https://cloud.google.com/sdk/downloads) for your OS, to deploy from your command line.

After the install process, we can use our google cloud account to create a new machine then connect to it and deploy containers. Let´s deploy our nginx site

follow https://developers.google.com/identity/protocols/application-default-credentials  and create GOOGLE_APPLICATION_CREDENTIALS system variable. 

```
$ docker-machine create -d google --google-project <google-cloud-project-id> <compute-name>
$ docker build -t mynginx .
$ docker run -d -p 80:80 mynginx

```
Go to Google Cloud console and select the virtual machine then active "Allow HTTP traffic"

Now we can check if the cointainer is up go to <http://<google-compute-ip>>

