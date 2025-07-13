
# Task 6 - Week 6: Configure Taints and Tolerants.

## Understanding Taints and Tolerations

In Kubernetes, Taints and Tolerations work together to ensure that pods are not scheduled onto inappropriate nodes and to allow pods to be scheduled on nodes that might otherwise repel them.

**Taints:** Applied to a node, a taint marks a node to repel a set of pods. Imagine a "no parking" sign on a specific parking spot. Unless a car (pod) has a special permit (toleration), it cannot park there. Taints are used to ensure that pods are not placed on nodes where they might consume specific resources, or where they might be disruptive.



**Tolerations:**Applied to a pod, a toleration allows the pod to be scheduled on nodes that have matching taints. It's like the "special permit" that allows a car to ignore the "no parking" sign. A pod with a toleration for a specific taint can be scheduled on a node with that taint, but it doesn't guarantee it will be.

Together, they steer pods away from or towards specific nodes.

---

## Step-by-Step Demonstration

### 1. Identifying the Node

First, I needed to identify the node in my Minikube cluster where I would apply the taint. Since I'm using Minikube, there's typically only one node, conveniently named minikube. I confirmed this using kubectl get nodes.

**Command:**  
```bash
kubectl get nodes
```

**Screenshot:**  
![Get Nodes](Images/imagestask6/Screenshot%202025-07-12%20213150.png)

---

### 2. Tainting the Node

With the node identified, I applied a taint to the minikube node. I used the command kubectl taint nodes minikube mykey=myvalue:NoSchedule.

- mykey=myvalue: This defines the key-value pair of the taint.

- NoSchedule: This is the effect of the taint. It means that no new pods will be scheduled on this node unless they have a matching toleration. Existing pods on the node are not affected.

This step effectively marked the minikube node as "off-limits" for pods that don't specifically "tolerate" this mykey=myvalue taint.

**Command:**  
```bash
kubectl taint nodes minikube mykey=myvalue:NoSchedule
```

**Screenshot:**  
![Taint Node](Images/imagestask6/Screenshot%202025-07-12%20213211.png)

---

### 3. Creating a Pod with Matching Toleration

Next, I created a YAML file named toleration-pod.yaml for a new pod. Crucially, this pod's specification included a tolerations section that matched the taint I just applied to the minikube node.

**YAML Used:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: toleration-pod
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations:
  - key: "mykey"
    operator: "Equal"
    value: "myvalue"
    effect: "NoSchedule"
```

**Command:**  
```bash
kubectl apply -f toleration-pod.yaml
```

**Screenshot:**  
![Apply Toleration Pod](Images/imagestask6/Screenshot%202025-07-12%20213353.png)

---

### 4. Verifying Pod Scheduling

After creating the toleration-pod, I verified where it was scheduled using kubectl get pods -o wide. The -o wide option provides additional details, including the NODE where the pod is running. As expected, the toleration-pod successfully scheduled and is running on the minikube node, despite the taint. This confirms that the toleration allowed it to bypass the NoSchedule effect of the taint.

**Command:**  
```bash
kubectl get pods -o wide
```

**Screenshot:**  
![Pod Scheduled](Images/imagestask6/Screenshot%202025-07-12%20213431.png)

**Screenshot:**  
![Pod Running](Images/imagestask6/Screenshot%202025-07-12%20213600.png)

---

### 5. Removing the Taint (Optional Cleanup)

Finally, as a good practice for cleanup or future operations, I removed the taint from the minikube node using kubectl taint nodes minikube mykey:NoSchedule-. The hyphen (-) at the end of the taint specification indicates removal. This allows the node to once again accept any pod for scheduling.

**Command:**  
```bash
kubectl taint nodes minikube mykey:NoSchedule-
```

**Screenshot:**  
![Remove Taint](Images/imagestask6/Screenshot%202025-07-12%20213549.png)

---

## Conclusion

This demonstration successfully illustrated the mechanics of Taints and Tolerations in Kubernetes. I showed how to:

- Taint a node to prevent general pod scheduling.
- Define a toleration in a pod's specification.
- Verify that a pod with a matching toleration can still be scheduled on a tainted node.

This capability is vital for scenarios such as dedicating nodes for specific workloads, preventing less critical pods from consuming resources on specialized nodes, or isolating problematic nodes. Understanding Taints and Tolerations is essential for effective Kubernetes cluster management and deployment strategies. 

---

