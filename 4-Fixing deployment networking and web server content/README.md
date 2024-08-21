### Walkthrough 


---   

### Problem Statement  

- For this lab, the HTTP service goat-service, which is part of the NGINX application goat deployed in the mountain namespace, should display the HTML contents of the configmap content.
- This means that an HTTP GET request to the goat-service should return the content of the configmap.
- You can port forward goat-service service, or use busybox or curl images to help you make HTTP requests to the service to debug.

---  

__Given Namespaces__   

![image](https://github.com/user-attachments/assets/0a042d3d-fca4-4f29-b94f-4740c20d023c)   

---   

__challenge-1) Service goat-service in mountain namespace has correct selectors.__  

Below is Deployment file selector.  

![image](https://github.com/user-attachments/assets/e9b837cc-4d88-4c2d-a22d-577adc16d9dd)    

Modify the selector of service  as below image  

![image](https://github.com/user-attachments/assets/680c07e1-c6c7-417f-9a39-466ef8bde20a)  


__challenge-2) Application goat is serving content from configmap content__   

given configmap content 

```
kubectl get configmap -n mountain
```

![image](https://github.com/user-attachments/assets/b349c4e1-7e9c-4c97-80e0-81552a65d7c9)

```
kubectl  edit configmap  content  -n mountain 
```

![image](https://github.com/user-attachments/assets/ec1ed05c-921a-4405-a280-e463db17e4f4)

Deployment File configuration  

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "2"
  creationTimestamp: "2024-08-21T05:40:09Z"
  generation: 2
  labels:
    app.kubernetes.io/name: goat
  name: goat
  namespace: mountain
  resourceVersion: "271756"
  uid: 17f25359-921d-40af-b820-02d721991d89
spec:
  progressDeadlineSeconds: 600
  replicas: 3
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app.kubernetes.io/name: goat
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app.kubernetes.io/name: goat
    spec:
      containers:
      - image: nginx:1.23
        imagePullPolicy: IfNotPresent
        name: exposed
        ports:
        - containerPort: 80
          name: web
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /usr/share/nginx/html/index.html      #path of index.html file  
          name: content-volume
          subPath: index.html
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
      volumes:
      name: content-volume
      - configMap:
          name: content
          items:
          - key: index.html          #assigned key in configmap (imp)    
            path: index.html
```

checking the configuration  

```
kubectl get svc -o wide
curl <service_ip>:8080
```

__Successfully completed the lab__ 

![image](https://github.com/user-attachments/assets/3edd404b-7f5b-4353-a9b3-24058211538a) 


