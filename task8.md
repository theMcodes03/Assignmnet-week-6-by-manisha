
# Kubernetes Task 8: Configure Health Probes for Pods

Hello! As a learner showcasing my work on GitHub, I'm delighted to present my task which demonstrates ***configuing health Probes for pods***.  This task reinforces how Kubernetes intelligently monitors the health of applications running within pods, ensuring their reliability and availability.

---

## Understanding Liveness & Readiness Probes (Revisited)

Just to quickly recap, these two types of probes are fundamental for robust application management in Kubernetes:

- **Liveness Probes**: These probes confirm if your application is still *running* and responsive. If an application becomes unresponsive (e.g., enters a deadlock or crashes internally), the liveness probe will fail, and Kubernetes will automatically **restart** the pod. This ensures your application doesn't get stuck in a bad state and recovers quickly.

- **Readiness Probes**: These probes determine if your application is *ready* to receive network traffic. An application might be running but still initializing (e.g., loading data, connecting to databases). If a readiness probe fails, Kubernetes temporarily **removes the pod from the Service endpoint list**, preventing traffic from being routed to it until it's truly ready. This avoids serving errors to users during application startup or temporary unhealthiness.

For this demonstration, I'm again using a straightforward **Nginx server** to illustrate these concepts.

---

## Step-by-Step Demonstration

### 1. Starting Minikube (Initial Setup)

My first step, as always, was to ensure my local Kubernetes environment, **Minikube**, was operational.

```
minikube start
```

### 2. Defining a Pod with Liveness and Readiness Probes

I created a YAML file named `health-probes-pod.yaml`.

![YAML File Content](Images/imagestask8/Screenshot%202025-07-12%20222425.png)


---

### 3. Applying the Pod Definition

```
kubectl apply -f health-probes-pod.yaml
```

![Apply YAML](Images/imagestask8/Screenshot%202025-07-12%20222540.png)


---

### 4. Verifying Pod Status

```
kubectl get pods
```

![Pod Status](Images/imagestask8/Screenshot%202025-07-12%20222613.png)


---

### 5. Describing the Pod for Probe Details

```
kubectl describe pod nginx-probe
```

![Describe Pod - Top](Images/imagestask8/Screenshot%202025-07-12%20222620.png)

![Describe Pod - Events](Images/imagestask8/Screenshot%202025-07-12%20222628.png)

![Describe Pod - Conditions](Images/imagestask8/Screenshot%202025-07-12%20222633.png)

---

## Conclusion

This exercise successfully demonstrated the configuration and initial verification of **Liveness and Readiness Probes** for a Kubernetes Pod. By implementing these health checks, we ensure that:

- The application within the `nginx-probe` pod is automatically **restarted** if it becomes unhealthy (liveness probe).
- The `nginx-probe` pod only receives traffic when it's genuinely **ready** to serve requests (readiness probe).

These probes are vital for creating resilient and highly available applications in a Kubernetes environment.

---
