
# Task 3 - Week 6: PersistentVolumes and PersistentVolumeClaims in Kubernetes

## Understanding Persistent Storage in Kubernetes: PVs and PVCs

This task delved into how Kubernetes manages persistent storage, which is crucial for applications that need to store data beyond the life of a single pod. I explored PersistentVolume (PV) and PersistentVolumeClaim (PVC) to provide durable storage for our applications. Here's a summary of what I learned: 

## Step-by-Step Breakdown and Analysis

### 1. Verifying Minikube Status 

**Command:** `minikube status`  
**Screenshot:**  
![Minikube Status](Images/imagestask3/Screenshot%202025-07-11%20203717.png)

Before doing anything, I made sure my Minikube cluster was up and running. The output confirmed that the Control Plane, host, kubelet, and apiserver were all Running, and kubeconfig was Configured. This is always the first logical step to ensure Kubernetes is ready for commands

---

### 2. Creating a PersistentVolume (PV) 

**Command:** `kubectl apply -f pv.yaml`  
**Screenshots:**  
![pv.yaml content](Images/imagestask3/Screenshot%202025-07-11%20203941.png)  
![PV Creation](Images/imagestask3/Screenshot%202025-07-11%20203732.png)

I started by defining a PersistentVolume using pv.yaml. This YAML file declared a PV named my-pv with 1 Gigabyte of storage and ReadWriteOnce access mode. Crucially, for Minikube, I specified a hostPath of /mnt/data.

The command kubectl apply -f pv.yaml created this PV. The purpose of a PV is to represent a piece of storage available in the cluster. It's like a physical hard drive that the cluster administrator makes available for applications to use.

---

### 3. Creating a PersistentVolumeClaim (PVC) 

**Command:** `kubectl apply -f pvc.yaml`  
**Screenshots:**  
![pvc.yaml content](Images/imagestask3/Screenshot%202025-07-11%20203946.png)  
![PVC Creation](Images/imagestask3/Screenshot%202025-07-11%20203732.png)

Next, I created a PersistentVolumeClaim using pvc.yaml. This YAML file defined a PVC named my-pvc, requesting 1 Gigabyte of storage with ReadWriteOnce access.

The command kubectl apply -f pvc.yaml created this PVC. A PVC is a request for storage by a user (or application). It's similar to requesting a specific amount of storage from an IT department. Kubernetes then tries to find an available PV that matches the PVC's requirements.

---

### 4. Verifying PV-PVC Binding 

**Command:** `kubectl get pv,pvc`  
**Screenshot:**  
![PV-PVC Binding](Images/imagestask3/Screenshot%202025-07-11%20203732.png)

After creating both the PV and PVC, I ran kubectl get pv,pvc to check their status. The output showed persistentvolume/my-pv and persistentvolumeclaim/my-pvc both with a STATUS of Bound.

This is a critical step: it confirms that Kubernetes successfully matched my my-pvc (the request) with my-pv (the available storage). This binding ensures that the storage requested by the application is now provisioned and ready for use.

---

### 5. Creating a Pod that Uses the PVC 

**Command:** `kubectl apply -f pod-pvc.yaml`  
**Screenshots:**  
![pod-pvc.yaml content](Images/imagestask3/Screenshot%202025-07-11%20203952.png)  
![Pod Creation](Images/imagestask3/Screenshot%202025-07-11%20203732.png)

With the storage successfully bound, I then deployed a Pod that would use this persistent storage. I used pod-pvc.yaml, which defines a Pod named pvc-pod running an Nginx container.

The crucial part here is the volumeMounts and volumes sections. The pod mounts the volume named my-storage at /usr/share/nginx/html inside the Nginx container. This my-storage volume is then linked to persistentVolumeClaim: claimName: my-pvc. This means the Nginx web server's default content directory will now be backed by the persistent storage we provisioned, ensuring any changes to the web content would persist even if the pod restarts or is rescheduled.

---

### 6. Ensuring the Pod is Running 

**Commands:**  
- `kubectl get pods`  
- `kubectl get pods pvc-pod`  

**Screenshots:**  
![Initial Pod Status](Images/imagestask3/Screenshot%202025-07-11%20203732.png)  
![Final Pod Running](Images/imagestask3/Screenshot%202025-07-11%20203815.png)

A subsequent kubectl get pods pvc-pod command (or just waiting a moment) confirmed that pvc-pod was Running with 1/1 containers ready, after 59 seconds. This indicates the pod successfully started and mounted the persistent volume.

---

### (Optional) Troubleshooting HostPath Issue 

If the Pod gets stuck in `ContainerCreating`:

```bash
minikube ssh
sudo mkdir -p /mnt/data
exit
kubectl delete pod pvc-pod
kubectl apply -f pod-pvc.yaml
```
This step is a common troubleshooting technique for hostPath volumes in Minikube. If the specified host path (/mnt/data in this case) does not exist inside the Minikube VM, the pod will fail to start because it cannot mount the volume. By SSHing into the Minikube VM and creating the directory, you resolve the underlying storage path issue, allowing the pod to start correctly after a re-apply. In my case, this wasn't strictly necessary for the final running state, but it's an important learning point for hostPath volumes.

---

## Final Status and Learning Outcomes

All Kubernetes objects related to persistent storage were successfully created and verified. The PV and PVC were bound, and the Nginx pod (`pvc-pod`) was successfully created and running with the mounted volume. This exercise demonstrated the process of creating and managing persistent storage in a Kubernetes cluster. 

**Key Concepts Learned:**

- **PersistentVolume (PV):** Represents the actual physical storage  capacity provisioned by an administrator.

- **PersistentVolumeClaim (PVC):** Represents a user's request for storage.

- **Binding Process:**  How Kubernetes automatically matches a PVC to an available PV.

- **Volume Mounting:**  How a pod then uses a PVC to mount the persistent storage at a specific path within its containers, making the data durable.

---

