# minikube-practice

#### Start minikube

The first step is to start the container of minikube where our cluster will be allocated:

```
minikube start
```

<img width="536" height="155" alt="Pasted image 20251003151650" src="https://github.com/user-attachments/assets/444c3a4e-574a-4603-a6ea-f88a7c3cb77e" />


#### Create deployment

Now is necessary to create the containers of  that will run the service, with the following command:

```
kubectl create deployment nginx --image=nginx --replicas=2
```
<img width="1050" height="51" alt="Pasted image 20251001165301" src="https://github.com/user-attachments/assets/c0c0decb-8e8c-4f31-9d6d-b889f56a4574" />
-  **--image:** Specified the image running in the container.
  
- **--replicas:** Specified the number of containers running the image inside the pod. 
It will create a pod and containers with the specified image, in this case nginx:


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
<img width="1298" height="57" alt="Pasted image 20251001165326" src="https://github.com/user-attachments/assets/3ed22331-fb4f-43ff-a8a5-48bf387b4c5f" />

<img width="769" height="104" alt="Pasted image 20251001165119" src="https://github.com/user-attachments/assets/1c2a9cdb-1107-4e42-be7a-2f16a921166f" />

<img width="1216" height="559" alt="Pasted image 20251003152233" src="https://github.com/user-attachments/assets/226aab30-9ed8-4f6b-829d-802301a4f91e" />


##### Nodeport

Now we create a new deployment to assign a different type of service:
<img width="1134" height="271" alt="Pasted image 20251005231435" src="https://github.com/user-attachments/assets/628fe6f6-e13b-4a6d-ab33-c82278a8fb4c" />

The type of service NodePort  allows connection between the nodes of the cluster:

```
kubectl expose deployment caddy --type=NodePort --port=80 --target-port=80
```

In this case it is possible to test the service using the command curl with the cluster ip and the port where the service is expose:

<img width="889" height="722" alt="Pasted image 20251005231603" src="https://github.com/user-attachments/assets/095013fd-4585-40e0-a6bf-5ef198e5baf4" />

##### LoadBalancer

We start with a new deployment:
```
kubectl create deployment alpine --image=nginx:alpine
```

This type of service is use to expose the service of a pod to external access, it is use only for one service:

<img width="912" height="293" alt="Pasted image 20251005234925" src="https://github.com/user-attachments/assets/4a5766fc-a2aa-4a78-becc-debe298ab2fe" />

<img width="911" height="195" alt="Pasted image 20251005235048" src="https://github.com/user-attachments/assets/51b2668d-5194-42c1-83d0-96f4995b3359" />
```
kubectl expose deployment alpine --type=LoadBalancer --port=80 --target-port=80
```


Now we expose the service with a tunnel to access it from the host machine:


<img width="1158" height="431" alt="Pasted image 20251005235129" src="https://github.com/user-attachments/assets/b5c0cbd8-43c6-4214-8f20-f736446573ce" />

<img width="1242" height="482" alt="Pasted image 20251005235155" src="https://github.com/user-attachments/assets/b0857978-18ea-4ccf-b9e2-e2a8ff3be7f3" />


### Ingress

Ingress is an API object that manage external access to services, to use it is necessary to enable the service. This will create a pod and container that run the service:

```
mnikube addons enable ingress
```

<img width="1214" height="451" alt="Pasted image 20251005232142" src="https://github.com/user-attachments/assets/a1094a90-bb63-4065-9760-42778083b7d5" />

Now we create another deployment to use it:

<img width="1188" height="328" alt="Pasted image 20251005232337" src="https://github.com/user-attachments/assets/ffc32625-b357-4a62-9e4b-11285a462b52" />

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
<img width="838" height="126" alt="Pasted image 20251005232915" src="https://github.com/user-attachments/assets/8a3b8c23-615e-478d-804d-c071f4d86ed2" />

Now we modify the archive *"/etc/hosts"* and add the minikube ip and the name of the  host:

<img width="339" height="40" alt="Pasted image 20251005233857" src="https://github.com/user-attachments/assets/5ed14962-f28a-4c6d-bad2-d35659c597db" />

And finally we curl the host's name:

<img width="591" height="80" alt="Pasted image 20251005233925" src="https://github.com/user-attachments/assets/27797639-cdfb-4a33-9d2c-07f3de0bf928" />

