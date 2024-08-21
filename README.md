# Key Topics Are added here 

---

- What is nodeSelector??  
  nodeselector helps kube-schedular to understand which pod has to schedule on which node. <br>
  nodeSelector is a hard match method.  
  
```yaml
nodeSelector:
   worker: green
```

- What is  Node Affinity?? <br>
  We tell to schedular that you can go with prefered configuration, if you found it then we can schedule on this if not then schedule on random node, no issue from my side.

```yaml
spec:
    containers:
    - name: my-app
    image: my-image
    affinity:
    nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
            - key: worker
            operator: In
            values:
            - green
```

 - What is Taints ??
   we taint node because we don't want to schedule anything on that particular node.

```bash
kubectl taint nodes <node_name>  key1=worker1:NoSchedule
```

- what is Tolerations ?? <br>
  We add toleration because we want our high priority pods still running on the Noschedule taints. <br>
  They are applied to pods and allow them to schedule onto nodes with matching taints. <br>
  They override the effect of taints.

```bash
spec:
  containers:
  - name: my-app
    image: my-image
  tolerations:
  - key: key1
    operator: Equal
    value: worker1
    effect: NoSchedule
```




