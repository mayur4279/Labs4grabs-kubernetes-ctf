## Walkthrough 
---  
### Problem Statement  
1. You have been assigned the task of diagnosing and resolving an issue with the monkey application in the **danger namespace**. The application is experiencing constant restarts, which suggests that there is a **problem with its probes**. To achieve the most efficient configuration, the probe needs to be configured with a **specific method** for checking opened ports of HTTP applications.
2. Furthermore, the monkey application has a **startup period of 15 seconds**. Your objective is to address these issues by configuring the probe to **prevent unnecessary restarts** and by delaying the initial probe check **by 15 seconds**.

---  

__Given Namespaces__  

![image](https://github.com/user-attachments/assets/a75bef18-d121-4e7b-aa4e-3d15d99cb7e1)

---   

__Challenge-1) Deployment monkey has correct method configured for livenessProbe && Deployment monkey has correctly configured port for livenessProbe.__  

Edit the deployment file using following command  
```
kubectl  edit deployment monkey  -n danger
```

Configure the livenessProbe as Below...   

![image](https://github.com/user-attachments/assets/369b5adb-f7e2-4c44-a6f3-8d9ded20ffd0)


__Challenge-2) Deployment monkey has startup delay correctly configured for livenessProbe.__   

For completing this task do following configuration...  

![image](https://github.com/user-attachments/assets/b41ada55-f397-4ef2-a57b-a658415cee49)     

Booooooooom!!!!!  Successfully completed the Lab  

![image](https://github.com/user-attachments/assets/51910ed4-915c-4d6b-82a6-14922084b9f2)  

