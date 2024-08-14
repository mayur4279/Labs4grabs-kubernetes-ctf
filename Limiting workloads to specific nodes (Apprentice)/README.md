## Walkthrough   

### Problem statement  
Make sure green deployment replicas in **green-ns** namespace only run on its designated green node, not on the **controlplane or red nodes**. </br> 
Additionally, you need to **create deployment red in red-ns namespace with five replicas**, _using stefanroman/kube-labs:sleep-app_ image. </br>
Deployment **red** should only run on **red node**. Additionally, the red node should exclusively run the red deployment and **reject any other workloads**. </br>   
---
### Given namespaces  
#### Image-1  
![image](https://github.com/user-attachments/assets/54a9c807-01bd-4717-9a29-cf238e2f0722)

---

### Challenge-1) Make sure green deployment replicas in green-ns namespace only run on its designated green node, not on the controlplane or red nodes.   
The green node is already tented thats why we cannot add pods inside the node green, in this case **we need to untaint the node green**

__Tainted poc__

![image](https://github.com/user-attachments/assets/93a661b4-6217-4cf7-818c-c3f63f4e77c1)  

__untainted screenshot__  

![image](https://github.com/user-attachments/assets/94ba9528-3ab5-4b53-a95f-77ec030f5ea2)   

__adding node selector in deployment in green-ns__   
label  is given  to the green node  </br>   

![image](https://github.com/user-attachments/assets/5859efba-fcdb-4910-be4a-cc486037c242)   

Adding nodeSelector in  deployment file of green namespace  

![image](https://github.com/user-attachments/assets/36cc7b9f-fa24-45cc-a3fb-37f040f677d2)

Successfully added all pods inside the green node using 

![image](https://github.com/user-attachments/assets/8aade13d-ed7a-46b1-824b-7b26c6db7274)


---  

### Challenge-2) Additionally, you need to create deployment red in red-ns namespace with five replicas, using stefanroman/kube-labs:sleep-app image.   

__creating new namespace__  
```
kubectl create ns red-ns
```
__used green deployment as a reference for creating deployment for the red namespace__  

```bash
kubectl get deployment green -n green-ns  -o yaml
vim  red-deployment.yaml  
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "2"
  creationTimestamp: "2024-08-14T17:18:55Z"
  generation: 2
  labels:
    app: red
  name: red
  namespace: red-ns
  resourceVersion: "7486"
  uid: 083318ad-fdca-4bb4-8ba5-da932d0428d7
spec:
  progressDeadlineSeconds: 600
  replicas: 5
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: red
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: red
    spec:
      containers:
      - image: stefanroman/kube-labs:sleep-app
        imagePullPolicy: IfNotPresent
        name: red
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      nodeSelector:
        worker: red
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```  

```bash
kubectl apply -f red-deployment.yaml  -n red-ns  
```  

__make sure to assign label to the red node__  

```
kubectl label nodes red  worker=red
```
__result__ 

![image](https://github.com/user-attachments/assets/8b6f910b-2f7a-4acb-9e41-38fcea1ba4bf)  

---   

### Challenge-3) Deployment red should only run on red node. Additionally, the red node should exclusively run the red deployment and reject any other workloads.

__we need to assign any taint to the red node to prevent other workloads from being scheduled on it.__   

```bash
kubectl taint nodes red key=value:NoSchedule
```

__Final Submition Boooooom__    

![image](https://github.com/user-attachments/assets/87178935-8feb-4ea9-9606-6ac6a2a0b6d2)

---  







