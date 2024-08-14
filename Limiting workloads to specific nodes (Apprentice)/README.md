## Walkthrough   

### problem statement  
Make sure green deployment replicas in **green-ns** namespace only run on its designated green node, not on the **controlplane or red nodes**. </br> 
Additionally, you need to **create deployment red in red-ns namespace with five replicas**, _using stefanroman/kube-labs:sleep-app_ image. </br>
Deployment **red** should only run on **red node**. Additionally, the red node should exclusively run the red deployment and **reject any other workloads**. </br>   

