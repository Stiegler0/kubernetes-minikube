# kubernetes-minikube

Minikube is a tool that lets you run Kubernetes locally. 
minikube runs a single-node Kubernetes cluster on your personal computer (including Windows, macOS and Linux PCs) so that you can try out Kubernetes, or for daily development work.

## Docker installation

### installation for Mac, Windows 10 Pro, Enterprise, or Education

https://www.docker.com/get-started

Choose Docker Desktop

![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/500d6c1b-356e-42ec-805a-fb2b37b9357e)


### installation for Windows home

https://docs.docker.com/docker-for-windows/install-windows-home/

## Kuberntes Minikube installation

https://minikube.sigs.k8s.io/docs/start/
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/929bf28f-b726-400a-bfda-9da81f73c3ee)

Minikube provides a dashboard (web portal). Access the dashboard using the following command:
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/db9b9491-6692-48a8-a25d-53d1012a9dc4)

```
minikube dashboard
```

## Download this project

This project contains a web service coded in Java, but the language doesn't matter. This project has already been built and the binary version is there:

First of all, download and uncompress the project: https://github.com/charroux/kubernetes-minikube

You can also use git: `git clone https://github.com/charroux/kubernetes-minikube`

Then move to the sud directory with `cd kubernetes-minikube/myservice` where a DockerFile is.

## Test this project using Docker

Compile the Java project:
```
./gradlew build
```
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/846ff1aa-1d54-4847-9222-b912e7a16630)

J'ai changé la version jdk utilisé (de 11  en 17) ensuite:
```
./gradlew --refresh-dependencies
```
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/1d8b95e4-bb70-4363-99c2-80f4cfa9469d)


Under Linux, or
```
.\gradlew build
```
Under Windows

Build the docker image:
```
docker build -t myservice .
```
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/bd1250a8-48b7-45f3-aa27-f5f08e0b5794)


Check the image:
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/2a826480-ec95-4d25-978e-026080c66fd9)

```
docker images
```

Start the container:
```
docker run -p 4000:8080 -t myservice
```
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/040ec4aa-f510-41f2-9d81-c10f340d4f72)


8080 is the port of the web service, while 4000 is the port for accessing the container. Test the web service using a web browser: http://localhost:4000 It displays hello.

Ctrl-C to stop the Web Service.

Check the containerID:
```
docker ps
```

Stop the container:
```
docker stop containerID
```

## Publish the image to the Docker Hub

Retreive the image ID:
```
docker images
```
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/49e49289-7594-4552-88c5-c90033fb8e49)


Tag the docker image: 
```
docker tag imageID yourDockerHubName/imageName:version
```

Example: `docker tag 1dsd512s0d myDockerID/myservice:1`
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/f682a7cf-52e9-4210-aceb-6a895b9c29ca)

Login to docker hub: 
```
docker login
```
or
```
docker login http://hub.docker.com
```
or 
```
docker login -u username -p password
```

Push the image to the docker hub:
```
docker push yourDockerHubName/imageName:version
```

Example: `docker push myDockerID/myservice:1`
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/d2713cc2-dacf-4b26-890b-467135b36cbd)

## Create a kubernetes deployment from a Docker image

```
kubectl get nodes
```
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/87658aca-8e95-421c-ad5b-99e9f56f31a9)

```
kubectl create deployment myservice --image=efrei/myservice:1
```
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/e992d5fc-8c3e-4d58-aa44-e99b02c0b604)

The image used comes from the Docker hub: https://hub.docker.com/r/efrei/myservice/tags

But you can use your own image instead.

Check the pod:
```
kubectl get pods
```
![image](https://github.com/Stiegler0/kubernetes-minikube/assets/145070468/ba8398fb-5537-4536-b07c-32c9044f9b54)


Check if the state is running.

Get complete logs for a pods: 
```
kubectl describe pods
```

Retreive the IP address but notice that this IP address is ephemeral since a pods can be deleted and replaced by a new one.

Then retrieve the deployment in the minikube dashboard. 
Actually the Docker container is runnung inside a Kubernetes pods (look at the pod in the dashboard).
  
You can also enter inside the container in a interactive mode with:
```
kubectl exec -it podname -- /bin/bash
```

where podname is the name of the pods obtained with:
```
kubectl get pods
```

List the containt of the container with:
```
ls
```

Don't forget to exit the container with:
```
exit
```

## Expose the Deployment through a service

A Kubernetes Service is an abstraction which defines a logical set of Pods running somewhere in the cluster, 
that all provide the same functionality. 
When created, each Service is assigned a unique IP address (also called clusterIP). 
This address is tied to the lifespan of the Service, and will not change while the Service is alive.

## Expose HTTP and HTTPS routes from outside the cluster to services within the cluster

For some parts of your application (for example, frontends) you may want to expose a Service onto an external IP address, that’s outside of your cluster.

Kubernetes ServiceTypes allow you to specify what kind of Service you want. The default is ClusterIP.

Type values and their behaviors are:

* ClusterIP: Exposes the Service on a cluster-internal IP. Choosing this value makes the Service only reachable from within the cluster. This is the default ServiceType.
* NodePort: Exposes the Service on each Node’s IP at a static port (the NodePort). A ClusterIP Service, to which the NodePort Service routes, is automatically created. You’ll be able to contact the NodePort Service, from outside the cluster, by requesting NodeIP:NodePort.
* LoadBalancer: Exposes the Service externally using a cloud provider’s load balancer. NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created.
* ExternalName: Maps the Service to the contents of the externalName field (e.g. foo.bar.example.com), by returning a CNAME record

## Expose HTTP and HTTPS route using NodePort

```
kubectl expose deployment myservice --type=NodePort --port=8080
```

Retrieve the service address:
```
minikube service myservice --url
```

This format of this address is `NodeIP:NodePort`.

Test this address inside your browser. It should display hello again.

Look from the NodeIP and the NodePort in the minikube dashboard.

## Scaling and load balancing

Check if the myservice deployment is running:

```
kubectl get deployments
```

How many instance are actually running:

```
kubectl get pods
```

Start a second instance:

```
kubectl scale --replicas=2 deployment/myservice
```
```
kubectl get deployments
```

and 

```
kubectl get pods
```

again

## Creating a Service of type LoadBalancer

Check if the myservice deployment is running:

```
kubectl get deployments
```

If a service is running in front of the deployment you must delete this service first in ordre to create a new one of kind LoadBalancer. So retreive the service using:

```
kubectl get services
```
And delete it:
```
kubectl delete service serviceName
```
```
kubectl expose deployment myservice --type=LoadBalancer --port=8080
```
```
minikube service myservice --url
```
Test in your web browser

## Create a deployment and a service using a yaml file

Yaml files can be used instead of using the command `kubectl create deployment` and `kubectl expose deployment`

The yaml file for the deployment: https://github.com/charroux/kubernetes-minikube/blob/main/myservice-deployment.yml

The yaml file for the node port service: https://github.com/charroux/kubernetes-minikube/blob/main/myservice-service.yml

The yaml file for the node port service: https://github.com/charroux/kubernetes-minikube/blob/main/myservice-loadbalancing-service.yml

Apply the deployment:
```
kubectl apply -f myservice-deployment.yml
```

Apply the node port service: 
```
kubectl apply -f myservice-service.yml
```

or 

Apply the service of type loadbalancer:
```
kubectl apply -f myservice-loadbalancing-service.yml
```
Then test if it works as expected.

# Routing rule to a service using Ingress

You can use Ingress to expose your Service. 
Ingress is not a Service type, but it acts as the entry point for your cluster. 
It lets you consolidate your routing rules into a single resource as it can expose multiple services under the same IP address.
Ingress exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. 
An Ingress may be configured to give Services externally-reachable URLs, load balance traffic, terminate SSL / TLS, and offer name-based virtual hosting.

## Set up Ingress on Minikube with the NGINX Ingress Controller

Enable the NGINX Ingress controller: 

```
minikube addons enable ingress
```
Verify that the NGINX Ingress controller is running:
```
kubectl get pods -n kube-system
```

Create a Deployment and expose it as a NodePort (not a loadbalancer).

Check if it works.

A yaml file for ingress: https://github.com/charroux/kubernetes-minikube/blob/main/ingress.yml

```
kubectl apply -f ingress.yml
```

Retrieve the IP address of Ingress: 

```
kubectl get ingress
```

```
NAME                 CLASS    HOSTS                  ADDRESS        PORTS   AGE

example-ingress      <none>   myservice.info         192.168.64.2   80      18m
```

On Linux: edit the `/etc/hosts` file and add at the bottom values for: 

`ADDRESS     HOSTS`

Then check in your Web browser: 

http://myservice.info/

On Windows : edit the `c:\windows\system32\drivers\etc\hosts` file, add 

`127.0.0.1 myservice.info`	

Enable a tunnel for Minikube:

```
minikube addons enable ingress-dns
```
```
minikube tunnel
```

Then check in your Web browser: 

http://myservice.info/


Create a second deployment and its service, then add a new route to the ingress.yml file.

## Delete resources

```
kubectl delete services myservice
```
```
kubectl delete deployment myservice
```

