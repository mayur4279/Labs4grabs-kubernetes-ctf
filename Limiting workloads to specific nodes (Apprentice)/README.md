## Walkthrough   

### problem statement  
Make sure green deployment replicas in **green-ns** namespace only run on its designated green node, not on the **controlplane or red nodes**. </br> 
Additionally, you need to **create deployment red in red-ns namespace with five replicas**, _using stefanroman/kube-labs:sleep-app_ image. </br>
Deployment **red** should only run on **red node**. Additionally, the red node should exclusively run the red deployment and **reject any other workloads**. </br>   

### Given info   
#### Image-1  
![image](https://github.com/user-attachments/assets/54a9c807-01bd-4717-9a29-cf238e2f0722)


### challenges1) Make sure green deployment replicas in green-ns namespace only run on its designated green node, not on the controlplane or red nodes.   
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







