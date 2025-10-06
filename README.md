# minikube-practice

#### Start minikube

The first step is to start the container of minikube where our cluster will be allocated:

```
minikube start
```
#### Start minikube

The first step is to start the container of minikube where our cluster will be allocated:
<img width="536" height="155" alt="Pasted image 20251003151650" src="https://github.com/user-attachments/assets/444c3a4e-574a-4603-a6ea-f88a7c3cb77e" />
```
minikube start
```

![[Pasted image 20251003151650.png]]
#### Create deployment

Now is necessary to create the containers of  that will run the service, with the following command:

```
kubectl create deployment nginx --image=nginx --replicas=2
```

-  **--image:** Specified the image running in the container.
- **--replicas:** Specified the number of containers running the image inside the pod. 
It will create a pod and containers with the specified image, in this case nginx:

<img width="536" height="155" alt="Pasted image 20251003151650" src="https://github.com/user-attachments/assets/f5a7f7dd-d9ab-4942-8ca6-cd6452bf5bc0" />

#### Services
Then, it is necessary to create the service to make accessible our containers inside the pod, there different type of service that we can define: 
##### ClusterIP

This type of service allows connectivity only between pods:
 ```
 kubectl expose deployment nginx --type=ClusterIP --port=80 --target-port=80
 ``` 
 - **--type:** Define the kind of service.
 - **--port:** The port use for communication between containers.
 - **--target-port:** The port the application runs.
 
To ensure that our service is running, it is necessary to access said service but the connectivity only works inside the cluster, to bypass that we will use port forwarding to expose the service in the localhost of the host :
```
kubectl port-forward svc/{service-name} 8080:80
```
<img width="1298" height="57" alt="Pasted image 20251001165326" src="https://github.com/user-attachments/assets/2243a059-121f-47e1-91d3-623fd87f6e31" />



##### Nodeport

Now we create a new deployment to assign a different type of service:
![[Pasted image 20251005231435.png]]
The type of service NodePort  allows connection between the nodes of the cluster:

```
kubectl expose deployment caddy --type=NodePort --port=80 --target-port=80
```

In this case it is possible to test the service using the command curl with the cluster ip and the port where the service is expose:

![[Pasted image 20251005231603.png]]
##### LoadBalancer

We start with a new deployment:
```
kubectl create deployment alpine --image=nginx:alpine
```

This type of service is use to expose the service of a pod to external access, it is use only for one service:

```
kubectl expose deployment alpine --type=LoadBalancer --port=80 --target-port=80
```


Now we expose the service with a tunnel to access it from the host machine:

![[Pasted image 20251005235129.png]]

![[Pasted image 20251005235155.png]]

### Ingress

Ingress is an API object that manage external access to services, to use it is necessary to enable the service. This will create a pod and container that run the service:

```
mnikube addons enable ingress
```

![[Pasted image 20251005232142.png]]

Now we create another deployment to use it:

![[Pasted image 20251005232337.png]]

For use the service ingress we need to define an YAML archive that specifies the port, path , host and service where ingress should redirect the traffic:

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: apache-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: apache.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-apache
            port:
              number: 80

```

After creating the file, we apply the settings that we define:

```
kubectl apply -f {archive name}
```

![[Pasted image 20251005232915.png]]
Now we modify the archive *"/etc/hosts"* and add the minikube ip and the name of the  host:
![[Pasted image 20251005233857.png]]

And finally with curl the host's name:

![[Pasted image 20251005233925.png]]

#### Create deployment

Now is necessary to create the containers of  that will run the service, with the following command:

```
kubectl create deployment nginx --image=nginx --replicas=2
```

-  **--image:** Specified the image running in the container.
- **--replicas:** Specified the number of containers running the image inside the pod. 
It will create a pod and containers with the specified image, in this case nginx:

![[Pasted image 20251001165301.png]]
#### Services
Then, it is necessary to create the service to make accessible our containers inside the pod, there different type of service that we can define: 
##### ClusterIP

This type of service allows connectivity only between pods:
 ```
 kubectl expose deployment nginx --type=ClusterIP --port=80 --target-port=80
 ``` 
 - **--type:** Define the kind of service.
 - **--port:** The port use for communication between containers.
 - **--target-port:** The port the application runs.
 
To ensure that our service is running, it is necessary to access said service but the connectivity only works inside the cluster, to bypass that we will use port forwarding to expose the service in the localhost of the host :
```
kubectl port-forward svc/{service-name} 8080:80
```

![[Pasted image 20251001165326.png]]

![[Pasted image 20251001165119.png]]

![[Pasted image 20251003152233.png]]
##### Nodeport

Now we create a new deployment to assign a different type of service:
![[Pasted image 20251005231435.png]]
The type of service NodePort  allows connection between the nodes of the cluster:

```
kubectl expose deployment caddy --type=NodePort --port=80 --target-port=80
```

In this case it is possible to test the service using the command curl with the cluster ip and the port where the service is expose:

![[Pasted image 20251005231603.png]]
##### LoadBalancer

We start with a new deployment:
```
kubectl create deployment alpine --image=nginx:alpine
```

This type of service is use to expose the service of a pod to external access, it is use only for one service:

```
kubectl expose deployment alpine --type=LoadBalancer --port=80 --target-port=80
```

![[Pasted image 20251005234925.png]]

![[Pasted image 20251005235048.png]]
Now we expose the service with a tunnel to access it from the host machine:

![[Pasted image 20251005235129.png]]

![[Pasted image 20251005235155.png]]

### Ingress

Ingress is an API object that manage external access to services, to use it is necessary to enable the service. This will create a pod and container that run the service:

```
mnikube addons enable ingress
```

![[Pasted image 20251005232142.png]]

Now we create another deployment to use it:

![[Pasted image 20251005232337.png]]

For use the service ingress we need to define an YAML archive that specifies the port, path , host and service where ingress should redirect the traffic:

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: apache-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: apache.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-apache
            port:
              number: 80

```

After creating the file, we apply the settings that we define:

```
kubectl apply -f {archive name}
```

![[Pasted image 20251005232915.png]]
Now we modify the archive *"/etc/hosts"* and add the minikube ip and the name of the  host:
![[Pasted image 20251005233857.png]]

And finally with curl the host's name:

![[Pasted image 20251005233925.png]]

