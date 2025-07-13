
# Task 7 - Create and Attach Persistent Volume Claims to Pods

Hello! As a learner sharing my work on GitHub, I'm excited to guide you through **Persistent Volumes (PVs)** and **Persistent Volume Claims (PVCs)** in Kubernetes. This is a fundamental concept for managing storage in your cluster, ensuring that your application data persists even if pods are restarted or moved.

## Understanding Persistent Volumes and Claims

In Kubernetes, pods are ephemeral by nature; they can be created, deleted, or rescheduled, meaning any data stored directly within a pod's container filesystem would be lost. This is where PVs and PVCs come into play:

- **Persistent Volume (PV)**: Think of a PV as a piece of actual storage in your cluster. It's a resource provisioned by an administrator (or dynamically provisioned by a StorageClass) that exists independently of any single pod. It could be a piece of disk space on a node, a cloud storage bucket, or a network file system. It's the physical storage available for use.

- **Persistent Volume Claim (PVC)**: A PVC is a request for storage by a user (or an application running in a pod). Kubernetes then finds a suitable PV that matches the PVC's requirements and binds them together.

This separation provides a flexible way to manage storage in Kubernetes, ensuring data persistence and decoupling storage from pod lifecycle.

## Step-by-Step Demonstration

### 1. Creating a Persistent Volume (PV)

My first step was to define a Persistent Volume. I created a pv.yaml file that specifies a 1 GiB hostPath volume, meaning it will use a directory on the node's filesystem (/mnt/data) as storage. The ReadWriteOnce access mode indicates it can be mounted as read-write by a single node.

**Screenshot:**  
![pv.yaml content](images/imagestask7//Screenshot%202025-07-12%20215732.png)  

I then applied this manifest using :  
```bash
kubectl apply -f pv.yaml
```  
**Screenshot:**  
![Apply PV and PVC](images/imagestask7//Screenshot%202025-07-12%20220228.png)

### 2. Creating a Persistent Volume Claim (PVC)

Next, I created a PersistentVolumeClaim (PVC) in pvc.yaml. This PVC requests 1 GiB of storage with ReadWriteOnce access mode. Kubernetes automatically finds and binds this PVC to the my-pv Persistent Volume I created in the previous step, as it matches the requirements.

**Screenshot:**  
![pvc.yaml content](images/imagestask7//Screenshot%202025-07-12%20220234.png)  

```bash
kubectl apply -f pvc.yaml
```  
**Screenshot (same as PV apply):**  
![Apply PV and PVC](images/imagestask7//Screenshot%202025-07-12%20220228.png)

### 3. Attaching the PVC to a Pod

Now that I had a PV and a bound PVC, I created a pod definition in pod-pvc.yaml. This pod is an Nginx container. The key part here is the volumeMounts section, which specifies that the my-storage volume (which is backed by my-pvc) should be mounted inside the container at /usr/share/nginx/html. This means that Nginx will serve content from this persistent location.

The pod definition in `pod-pvc.yaml` uses the PVC:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-using-pvc
spec:
  containers:
  - name: my-container
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: my-storage
  volumes:
  - name: my-storage
    persistentVolumeClaim:
      claimName: my-pvc
```

```bash
kubectl apply -f pod-pvc.yaml
```  
**Screenshot:**  
![Apply pod-pvc.yaml](images/imagestask7//Screenshot%202025-07-12%20220329.png)

### 4. Verifying the Setup

- This command showed that my-pv exists and its status, specifically if it's Bound to a claim. You can see it's indeed Bound to default/my-pvc.

```bash
kubectl get pv
```  
**Screenshot:**  
![Get PV](images/imagestask7//Screenshot%202025-07-12%20220343.png)

- This confirmed that my-pvc is also Bound to a volume.

```bash
kubectl get pvc
```  
**Screenshot:**  
![Get PVC](images/imagestask7//Screenshot%202025-07-12%20220403.png)

- This showed that pod-using-pvc is Running and 1/1 ready.

```bash
kubectl get pod
```  
**Screenshot:**  
![Get Pod](images/imagestask7//Screenshot%202025-07-12%20220420.png)

```bash
kubectl describe pod pod-using-pvc
```  
**Screenshots:**  
![Describe Pod - Top](images/imagestask7//Screenshot%202025-07-12%20220446.png)  
![Describe Pod - Mid](images/imagestask7//Screenshot%202025-07-12%20220456.png)  
![Describe Pod - Events](images/imagestask7//Screenshot%202025-07-12%20220505.png)

## Conclusion

This demonstration successfully showcased the process of creating and utilizing Persistent Volumes and Persistent Volume Claims in Kubernetes. By leveraging these constructs, applications can achieve data persistence, meaning their data remains available even if the pods themselves are recreated, relocated, or deleted. This is fundamental for stateful applications in a dynamic containerized environment.
