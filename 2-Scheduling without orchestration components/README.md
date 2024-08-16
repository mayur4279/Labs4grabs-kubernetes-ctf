## Walkthrough
--- 
### Problem Statement  
To complete this challenge, you need to do the following tasks: 
1. Schedule the backend pod in the big namespace to **node2**.
2. Upgrade the running container in the static database-controlplane pod in the big namespace to use **postgres:13 image**.
Your manager also instructed you to **deploy an extra static pod** using **frontend.yaml** filename to **node1**. The pod should be named frontend and should be deployed to the big namespace. Use the nginx:1.23 image for the pod.
---  

### Given namespaces   
![image](https://github.com/user-attachments/assets/761f3b3e-fa8a-4e59-83b0-39366bac7494)    
---   

__Challenge-1)  Schedule the backend pod in the big namespace to node2__

Create a copy of pod manifest file  
```
kubectl get pod backend -n big  -o   yaml   > backend-pod.yaml    
```   

Delete the pod  
```
kubectl delete pod  backend  -n big
```

Edit backend-pod.yaml  file as below  

![image](https://github.com/user-attachments/assets/d6913dbb-560e-47bb-8440-be3de89797d1)

apply using following command  

```
kubectl  apply -f backend-pod.yaml
```

Pod is Successfully able to run on node2  with **nodeName**    

![image](https://github.com/user-attachments/assets/6e8e23d8-88b6-43f7-9953-957ee58b53d6)   


__Challenge-2) Upgrade the running container in the static database-controlplane pod in the big namespace to use postgres:13 image__   

We need to edit the pod manifest file using following command  

```
kubectl  edit pod database-controlplane -n big
```

Update the version as per below image 

![image](https://github.com/user-attachments/assets/a67dee1c-1605-4d08-855c-3a58f60b8f40)


__Challenge-3) Your manager also instructed you to  deploy an extra static pod using frontend.yaml filename to node1. The pod should be named frontend and should be deployed to the big namespace. Use the nginx:1.23 image for the pod.__   


Create a static pod using nginx:1.23 image with big namespace.   

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: frontend
  namespace: big 
spec:
  nodeName: node1  
  containers:
  - name: frontend
    image: nginx:1.23
    resources:  {}
    ports:
      - containerPort: 80   
``` 

Shift the node1 using following command  

```
ssh node1
```
![image](https://github.com/user-attachments/assets/1640e5b0-046d-4fc8-a66a-d222703ab5d4)

Change the directory and create frontend.yaml  file  

```
cd /etc/kubernetes/manifest/
vim  frontend.yaml  
```

Add the manifest file which we created already....   
Just save it, it will create Frontend pod Automatically... Booooooom.....

![image](https://github.com/user-attachments/assets/50556dc3-da05-4e29-aa97-e82f8094fa2f)

Successfully completed the lab...  

![image](https://github.com/user-attachments/assets/24c53973-9332-4ede-90e9-f301f8ed647c)


