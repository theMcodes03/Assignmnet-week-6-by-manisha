# Task 2 - Week 5: Kubernetes service types (ClusterIP, NodePort, LoadBalancer)

## 1. Minikube Cluster Initialization 

**Screenshot:**  
![Minikube Start](Images/imagestask2/Screenshot%202025-07-11%20113257.png)

**Description:**  
This screenshot confirms the successful start of the Minikube cluster. Minikube initiated a local Kubernetes cluster on Windows using the Docker driver. The output indicates the Minikube version (v1.33.1) and confirms that kubectl is now configured to use this newly started cluster.
---

## 2. Kubernetes Cluster Information & Service Creation 

**Screenshots:**  
![Cluster Info](Images/imagestask2/Screenshot%202025-07-11%20114116.png)  
![Service YAMLs](Images/imagestask2/Screenshot%202025-07-11%20114122.png)

**Description:**  
These screenshots depict two main stages:

- `kubectl cluster-info`: Confirms that the Kubernetes control plane is running at `https://127.0.0.1:50850` and shows the status of CoreDNS.
- Applying YAMLs via `kubectl apply -f`:
  - `cluster-ip.yaml`: Creates a ClusterIP service.
  - `nodeport-app.yaml`: Creates a NodePort service.
  - `nodeport-app1.yaml`: Similar NodePort, possibly unchanged.
  - `loadbalancer.yaml`: Creates a LoadBalancer service (pending in Minikube unless configured).

---

## 3. Verification of All Kubernetes Resources 

**Screenshot:**  
![kubectl get all](Images/imagestask2/Screenshot%202025-07-11%20114144.png)

**Description:**  
This shows output from `kubectl get all`, listing all services including:
- `myapp-clusterip`
- `myapp-loadbalancer` (pending external IP)
- `myapp-nodeport` (NodePort 30080)
- default `kubernetes` service

---

## 4. Pod Creation and Status Check 

**Screenshots:**  
![Pod Creation](Images/imagestask2/Screenshot%202025-07-11%20114848.png)  
![Pod Status Top](Images/imagestask2/Screenshot%202025-07-11%20115115.png)  
![Pod Status Full](Images/imagestask2/Screenshot%202025-07-11%20115107.png)

**Description:**  
Illustrates:
- `kubectl apply -f pod.yaml`: Creates `myapp-pod` (Nginx).
- `kubectl get pods`: Confirms `Running` state with uptime. 
- `kubectl describe pod myapp-pod`: Detailed pod status, including events and logs.
- `kubectl logs myapp-pod`: Displays pod logs.
- `kubectl exec -it myapp-pod -- /bin/bash`: Interactive shell within the pod
- `kubectl port-forward myapp-pod 8080:80 &`: Forwarding port
- `curl http://localhost:8080`: Accessing the pod's service from the host machine

---

## 5. Port Forwarding and Application Access 

**Screenshots:**  
![Port Forward Start](Images/imagestask2/Screenshot%202025-07-11%20115128.png)  
![Browser Output](Images/imagestask2/Screenshot%202025-07-11%20115213.png)

**Description:**  
- `kubectl port-forward svc/myapp-clusterip 8080:80`: Forwards local port 8080 to service port 80.
- Browser output (`http://localhost:8080`) shows **"Welcome to nginx!"**, confirming pod accessibility from the host. 
- `kubectl port-forward svc/myapp-loadbalancer 8080:80`: Similar forwarding for LoadBalancer service (pending external IP).
- `kubectl port-forward svc/myapp-nodeport 8080:80`: NodePort service forwarding , accessible from the host machine.
- `kubectl port-forward svc/kubernetes 8080:80`: Forwarding the default Kubernetes service.
