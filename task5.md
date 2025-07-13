# Task 5 - Week 6: Configure liveliness and readiness probes for pods in AKS cluster.

## Kubernetes Liveness and Readiness Probes

## Understanding Liveness and Readiness Probes

**Liveness Probe**:
 Imagine your application gets stuck in a state where it's running but not actually processing requests (e.g., a deadlock). A liveness probe checks if the application is still alive and functioning correctly. If the probe fails, Kubernetes will automatically restart the pod, giving your application a fresh start. This helps maintain the application's availability.
 or in less words, 
 Checks if the app is still alive. If it fails, the pod is restarted automatically.

**Readiness Probe**:
 When an application starts, it might take some time to initialize, load data, or establish connections before it's ready to serve traffic. A readiness probe checks if the application is ready to accept incoming requests. If the probe fails, Kubernetes temporarily removes the pod from the Service's endpoint list, preventing traffic from being routed to an unready pod. Once the probe passes, the pod is added back, ensuring only healthy pods receive traffic.
or in less words, 
Checks if the app is ready to serve traffic. If it fails, the pod is removed from the service endpoints temporarily.

This task uses an Nginx container to demonstrate both types of probes.

---

## Step-by-Step Demonstration

### 1. Starting Minikube
**Command**: `minikube start`  
![Start Minikube](Images/imagestask5/Screenshot%202025-07-12%20205417.png)

My first step was to ensure my local Kubernetes environment, Minikube, was up and running. Minikube allows me to run a single-node Kubernetes cluster on my personal machine, perfect for development and testing.

---

### 2. Creating a Pod with Probes
**Command**: `kubectl apply -f task5-data.yaml`  
![Apply YAML](Images/imagestask5/Screenshot%202025-07-12%20205654.png)

Next, I defined my Kubernetes Pod configuration in a YAML file named task5-data.yaml. This file specifies an Nginx container and configures both readinessProbe and livenessProbe.

Readiness Probe: This probe uses httpGet to check the root path (/) on port 80. It has an initialDelaySeconds of 5 seconds (meaning it waits 5 seconds after the container starts before the first check) and a periodSeconds of 5 seconds (meaning it checks every 5 seconds).

Liveness Probe: Similar to the readiness probe, it also uses httpGet on the root path and port 80. However, it has a longer initialDelaySeconds of 10 seconds and also checks every 5 seconds.

---

### 3. Initial Pod Status Check
**Command**: `kubectl get pods`  
![Initial Pod Status](Images/imagestask5/Screenshot%202025-07-12%20205740.png)

After applying the YAML, I checked the status of the newly created probe-demo pod using kubectl get pods. Initially, you can see the pod is created, but the READY status might not immediately be 1/1 as the readiness probe has an initial delay.

---

### 4. Describe Pod for Probe Details
**Command**: `kubectl describe pod probe-demo`  
![Describe Pod](Images/imagestask5/Screenshot%202025-07-12%20205800.png)
 
To get a more detailed view of the pod, including the configured probes and recent events, I used kubectl describe pod probe-demo. This command provides valuable information, such as the Liveness and Readiness probe configurations and the lifecycle events of the pod, confirming that Nginx was successfully pulled and the container started.

![Pod Events](Images/imagestask5/Screenshot%202025-07-12%20205800.png)

---

### 5. Breaking Liveness Probe
**Command**: 
```bash
kubectl exec -it probe-demo -- /bin/bash
rm /usr/share/nginx/html/index.html
```
![Break Liveness Probe](Images/imagestask5/Screenshot%202025-07-12%20205855.png)

This was the most exciting part! To demonstrate the liveness probe in action, I intentionally "broke" the Nginx server by deleting its default index.html file from inside the probe-demo container. This causes the httpGet liveness probe to fail, as there's no content to serve.

---

### 6. Observe Pod Restarts
**Command**:
```bash
kubectl get pods -w
``` 
![Pod Restarts](Images/imagestask5/Screenshot%202025-07-12%20205924.png)

After deleting the index.html file, I used kubectl get pods -w to continuously watch the pod's status. As expected, after a short period, the RESTARTS count for probe-demo incremented. This clearly shows that the liveness probe detected the application's unhealthy state and Kubernetes automatically restarted the pod to restore its functionality.

---

## Conclusion

- This demonstration effectively illustrates the critical role of Liveness and Readiness Probes in maintaining the stability and availability of applications within Kubernetes.

- The liveness probe successfully ensured that my Nginx application was restarted when it became unresponsive, preventing it from getting stuck in a bad state.

- The readiness probe (though not explicitly broken in this demo) would ensure that traffic is only directed to the Nginx server once it's fully ready to serve requests, preventing users from encountering errors due to an uninitialized application.

- By implementing these probes, we can build more resilient and robust applications on Kubernetes!
---

