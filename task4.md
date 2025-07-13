
# Task 4: Managing Kubernetes with Azure Kubernetes Service (AKS)

This task focuses on managing Kubernetes clusters using Microsoft Azure's Kubernetes Service (AKS). It covers creating a cluster, configuring access, scaling attempts, and applying health probes to pods.

---

## 1. AKS Cluster Creation

I began by creating an AKS cluster named myAKSCluster in the aks-rg-by-m resource group. I configured it with two nodes and enabled monitoring.

**Command:**
```bash
az aks create --resource-group aks-rg-by-m --name myAKSCluster --node-count 2 --enable-addons monitoring --generate-ssh-keys --node-vm-size Standard_B2s
```

**Verification:**
```bash
az aks list --resource-group aks-rg-by-m -o table
```

**Impact:**  
A new AKS cluster was provisioned in Azure with two nodes and monitoring enabled.

![Creating AKS Cluster](Images/imagestask4/Screenshot%202025-07-12%20230719.png)  
![Verifying AKS Cluster Creation](Images/imagestask4/Screenshot%202025-07-12%20230731.png)

---

## 2. Configure kubectl to Access Cluster

To interact with the newly created AKS cluster from my local machine, I needed to fetch its credentials and merge them with my kubeconfig file.

**Command:**
```bash
az aks get-credentials --resource-group aks-rg-by-m --name myAKSCluster
```

**Impact:**  
Merged AKS credentials into local kubeconfig, enabling kubectl access.

![Fetching AKS Credentials](Images/imagestask4/Screenshot%202025-07-12%20230748.png)

---

## 3. Verified AKS Cluster Nodes

After configuring kubectl, I verified that the nodes in my AKS cluster were ready and accessible.

**Command:**
```bash
kubectl get nodes
```

**Impact:**  

 Confirmed that the two nodes provisioned with the AKS cluster were in a Ready state, indicating they are prepared to run pods.

![Verifying AKS Nodes](Images/imagestask4/Screenshot%202025-07-12%20230803.png)

---

## 4. Attempted Scaling the Cluster

I attempted to scale the AKS cluster from 2 to 3 nodes. However, this operation encountered an issue due to Azure quota limitations in the specified region.

**Command:**
```bash
az aks scale --resource-group aks-rg-by-m --name myAKSCluster --node-count 3
```

**Impact:**  
The operation failed due to Azure quota limitations in the region.

![Attempting to Scale AKS Cluster](Images/imagestask4/Screenshot%202025-07-12%20230819.png)

---

## 5. Created YAML for Probes (In Cloud Shell)

To ensure application reliability, I defined a Kubernetes Pod configuration (nginx-probes.yaml) that includes liveness and readiness probes. These probes periodically check the health of the Nginx container.

**Command:**
```bash
nano nginx-probes.yaml
```

**YAML Highlights:**

- **Liveness Probe**: Uses `httpGet` on `/`, port 80, checks every 10s after an initial delay of 10s.
- **Readiness Probe**: Uses `httpGet` on `/`, port 80, checks every 5s after an initial delay of 3s.

**Impact:**  
Defined Kubernetes probes for managing pod health and traffic readiness.

![Creating nginx-probes.yaml with Health Probes](Images/imagestask4/Screenshot%202025-07-12%20230834.png)

---

## 6. Applied the YAML

After creating the YAML, I applied it to the AKS cluster to deploy the Nginx pod with the configured health probes.

**Command:**
```bash
kubectl apply -f nginx-probes.yaml
```

**Impact:**  
Deployed nginx-probe-pod in the cluster with the configured health probes.

![Applying nginx-probes.yaml](Images/imagestask4/Screenshot%202025-07-12%20230849.png)

---

## 7. Verified Pod Health and Events

Finally, I inspected the status and events of the deployed pod to confirm that the health probes were functioning as expected and the pod was running correctly.

**Commands:**
```bash
kubectl get pods
kubectl describe pod nginx-probe-pod
```

**Impact:**  
Confirmed pod readiness and health status through probe success events and condition reports.

![Verifying Pod Status](Images/imagestask4/Screenshot%202025-07-12%20230904.png)  
![Describing Pod to Check Probes](Images/imagestask4/Screenshot%202025-07-12%20230920.png)  
![Pod Conditions Showing True](Images/imagestask4/Screenshot%202025-07-12%20230928.png)  
![Pod Events (Pulling, Created, Started)](Images/imagestask4/Screenshot%202025-07-12%20230933.png)

---

## Summary

I successfully completed Task 4, demonstrating AKS cluster management from creation and access configuration to implementing essential liveness and readiness probes. While a scaling attempt hit an Azure quota limit, the core objectives of deploying a healthy application with robust health checks in AKS were met, reinforcing practical skills for cloud-native deployments. The hands-on experience with YAML configuration, probe setup, and pod management will be valuable for future cloud computing endeavors. 