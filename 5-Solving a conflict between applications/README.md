## Walkthrough 
---  

### Problem Statement 
- Your task is to ensure that the **nginx and httpd services** in the gel namespace are functioning properly **without directly editing or replacing the service objects.**
- The services connect to the **hair** application in the **gel** namespace, which is currently having a problem.
- Your job is to find and fix the issue, which might bring the services back to normal.

---   

__Given Namespaces__  

![image](https://github.com/user-attachments/assets/3e28d6bd-a54a-488d-b74e-f23b8fc5fd36)

__Given Deployment file__   

```
kubectl  get deploy hair -n gel  -o yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2024-08-21T07:04:51Z"
  generation: 1
  labels:
    app.kubernetes.io/name: hair
  name: hair
  namespace: gel
  resourceVersion: "1263"
  uid: 336849fc-9b68-4080-b788-f34355d7dd07
spec:
  progressDeadlineSeconds: 600
  replicas: 5
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app.kubernetes.io/name: hair
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app.kubernetes.io/name: hair
    spec:
      containers:
      - image: nginx:1.23
        imagePullPolicy: IfNotPresent
        name: nginx
        ports:
        - containerPort: 80
          name: nginx
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      - image: httpd:2.4.57
        imagePullPolicy: IfNotPresent
        name: httpd
        ports:
        - containerPort: 80
          name: httpd
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```

__Given service.yaml file of httpd && nginx service__   

```bash
kubectl  get svc  -n gel   -o yaml
```  
```yaml
apiVersion: v1
items:
- apiVersion: v1
  kind: Service
  metadata:
    creationTimestamp: "2024-08-21T07:04:51Z"
    name: httpd
    namespace: gel
    resourceVersion: "877"
    uid: c6415a9b-0ed5-44f9-883d-1a2ee7f5c129
  spec:
    clusterIP: 10.107.241.150
    clusterIPs:
    - 10.107.241.150
    internalTrafficPolicy: Cluster
    ipFamilies:
    - IPv4
    ipFamilyPolicy: SingleStack
    ports:
    - port: 80
      protocol: TCP
      targetPort: 8080
    selector:
      app.kubernetes.io/name: hair
    sessionAffinity: None
    type: ClusterIP
  status:
    loadBalancer: {}
- apiVersion: v1
  kind: Service
  metadata:
    creationTimestamp: "2024-08-21T07:04:51Z"
    name: nginx
    namespace: gel
    resourceVersion: "874"
    uid: 138ef6e9-eb38-425c-a129-0c3bf46e3485
  spec:
    clusterIP: 10.102.40.231
    clusterIPs:
    - 10.102.40.231
    internalTrafficPolicy: Cluster
    ipFamilies:
    - IPv4
    ipFamilyPolicy: SingleStack
    ports:
    - port: 80
      protocol: TCP
      targetPort: 80
    selector:
      app.kubernetes.io/name: hair
    sessionAffinity: None
    type: ClusterIP
  status:
    loadBalancer: {}
kind: List
metadata:
  resourceVersion: ""
```

---   


__Challenge-1) Running content of /usr/local/apache2/conf/httpd.conf file from configmap__   

**why we are doing this??**
- **Because, we have two containers in one Pod where nginx and httpd services by default run on port 80.**
- **to avoid this conflict we need to edit httpd.conf file to run httpd server on any port (8080 is used here).**
  
Create httpd pod for getting deafult configurtion.  

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
  labels:
    name: myapp
spec:
  containers:
  - name: myapp
    image: httpd
    resources:
      limits:
        memory: "128Mi"
        cpu: "500m"
    ports:
      - containerPort: 80 
```

Apply using following command  

```
kubectl  apply -f pod.yaml
```

Use following command for connecting with pod 

```
kubectl  exec -it my-app -n  /bin/bash
```

**Cat And copy the content of following file "/usr/local/apache2/conf/httpd.conf" <br>**
**Make Sure to edit Default port 80  --> 8080   <br>** 

![image](https://github.com/user-attachments/assets/57450591-6bea-4793-8a19-7199ebc85a40)   


Use following command for Creating the configmap in gel namespace

```
kubectl create configmap httpd-config --from-file=httpd.conf -n gel    
```

Edit Deployment file configuration as below 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "3"
  creationTimestamp: "2024-08-21T04:32:13Z"
  generation: 3
  labels:
    app.kubernetes.io/name: hair
  name: hair
  namespace: gel
  resourceVersion: "190951"
  uid: 9df2f63b-9ed1-4166-974c-0309050ca8d3
spec:
  progressDeadlineSeconds: 600
  replicas: 5
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app.kubernetes.io/name: hair
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app.kubernetes.io/name: hair
    spec:
      containers:
      - image: nginx:1.23
        imagePullPolicy: IfNotPresent
        name: nginx
        ports:
        - containerPort: 80
          name: nginx
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      - image: httpd:2.4.57
        imagePullPolicy: IfNotPresent
        name: httpd
        ports:
        - containerPort: 8080                              #Port is Changed here  
          name: httpd
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /usr/local/apache2/conf/httpd.conf
          name: httpd-config-volume
          subPath: httpd.conf
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
      volumes:
      - configMap:
          defaultMode: 420
          items:
          - key: httpd.conf
            path: httpd.conf
          name: httpd-config
        name: httpd-config-volume
```

__Result__ 

- Service httpd is serving default content.

![image](https://github.com/user-attachments/assets/bc271e26-9dd1-4bd2-b27c-a3c0df6f9518)

- Service nginx is serving default conten.

![image](https://github.com/user-attachments/assets/971f1515-19c2-4d13-ad7b-a22dbf1c4f83)  

- **Successfully Completed the Lab**

![image](https://github.com/user-attachments/assets/6ba5cd4f-5956-4436-90c1-0eb2e6b18685)    



