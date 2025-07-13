
# Kubernetes Task 9: Configure Autoscaling in Your Cluster (Horizontal Scaling)

Hello! As a learner sharing my work on GitHub, I'm excited to present my demonstration of **Horizontal Pod Autoscaling (HPA)** in Kubernetes. This is a powerful feature that automatically adjusts the number of pod replicas in a deployment based on observed CPU utilization or other custom metrics, ensuring your applications can handle varying loads efficiently. Let's dive into the configuration process. 

---

## Understanding Horizontal Pod Autoscaling (HPA)

In dynamic environments, application workloads can fluctuate significantly. Manually adjusting the number of running pods to meet demand is inefficient and reactive. This is where HPA shines: 

- **Objective**: HPA automatically scales the number of pod replicas in a Deployment (or ReplicationController, ReplicaSet, StatefulSet) up or down. This is based on the observed CPU utilization or other custom metrics, ensuring your application can handle varying loads efficiently.
- **Mechanism**: It continuously monitors specified metrics (commonly CPU utilization, but can also use memory or custom metrics). If the average utilization crosses a defined threshold, HPA instructs the Kubernetes controller to increase or decrease the number of replicas, keeping the application responsive and efficient. 
- **Benefits**:
  - **Elasticity**: Applications can handle sudden spikes in traffic without manual intervention. 
  - **Cost-Efficiency**: Resources are only consumed when needed, preventing over-provisioning during low-demand periods. 
  - **Reliability**: Ensures consistent performance by maintaining adequate resources. 

For this demonstration, I've configured HPA to scale an Nginx deployment based on **CPU utilization**.

---

## Step-by-Step Demonstration

### 1. Enable Metrics Server (Prerequisite)

The Horizontal Pod Autoscaler relies on metrics to make scaling decisions. Kubernetes needs a **Metrics Server** to collect these metrics (like CPU and memory usage) from nodes and pods. If you haven't already, enable the Metrics Server in your cluster: 

```
minikube addons enable metrics-server
```

![Enable Metrics Server](Images/imagestask9/Screenshot%202025-07-12%20232633.png)

After enabling, I verified that the `metrics-server` deployment was running successfully in the `kube-system` namespace:

```
kubectl get deployment metrics-server -n kube-system
```

![Verify Metrics Server](Images/imagestask9/Screenshot%202025-07-12%20232649.png)

---

### 2. Create a Sample Deployment and Expose It

I created a simple Nginx Deployment named my-app with a single replica. This is the application that HPA will manage.

```
kubectl create deployment my-app --image=nginx --replicas=1
```

![Create Deployment](Images/imagestask9/Screenshot%202025-07-12%20232718.png)

Then, I exposed this deployment as a NodePort Service. This makes the Nginx application accessible from outside the cluster, which is essential for generating load later.

```
kubectl expose deployment my-app --port=80 --type=NodePort
```

![Expose Deployment](Images/imagestask9/Screenshot%202025-07-12%20232732.png)

---

### 3. Apply Resource Requests and Limits

For HPA to effectively scale based on CPU, the pods in the deployment need to have resource requests and limits defined. This tells Kubernetes how much CPU the container needs (request) and how much it can use (limit). I patched the my-app deployment to add CPU requests of 100m (100 millicores) and limits of 200m.

```
kubectl patch deployment my-app -p '{"spec":{"template":{"spec":{"containers":[{"name": "nginx","resources":{"requests":{"cpu": "100m"},"limits":{"cpu": "200m"}}}]}}}}'
```

![Patch Deployment](Images/imagestask9/Screenshot%202025-07-12%20232853.png)

---

### 4. Create the Horizontal Pod Autoscaler (HPA)

This is the core step! I created the HPA for my my-app deployment using the kubectl autoscale command:
This command configures the HPA to:

- Target the my-app deployment.

- Maintain an average CPU utilization of 50%.

- Scale the number of replicas between a minimum of 1 and a maximum of 5.

I then verified the HPA's creation and current status using kubectl get hpa. Initially, the TARGETS might show <unknown>/50% as metrics are still being collected.

```
kubectl autoscale deployment my-app --cpu-percent=50 --min=1 --max=5
```

![Create HPA](Images/imagestask9/Screenshot%202025-07-12%20232921.png)

Verify the HPA:

```
kubectl get hpa
```

![Get HPA](Images/imagestask9/Screenshot%202025-07-12%20232932.png)

---

### 5. Generate Load (to Trigger Autoscaling)

To see the HPA in action, I needed to simulate traffic and increase the CPU utilization of the Nginx pod. I did this by running a busybox pod and continuously sending requests to the my-app service.

```
kubectl run -i --tty load-generator --image=busybox /bin/sh
```

Inside the pod, run:

```
while true; do wget -q -O- http://my-app; done
```

![Generate Load](Images/imagestask9/Screenshot%202025-07-12%20233011.png)

---

### 6. Observe Scaling

With the load generator running, I monitored the HPA using kubectl get hpa -w. The -w (watch) flag allowed me to see real-time updates.

I observed the TARGETS CPU utilization gradually increasing and, once it surpassed the 50% threshold, the HPA automatically increased the REPLICAS (from 1 to 2, and potentially more depending on the load), demonstrating successful horizontal scaling!

```
kubectl get hpa -w
```

![Observe Scaling](Images/imagestask9/Screenshot%202025-07-12%20233144.png)

---

## Conclusion

This demonstration effectively showcased the power of Horizontal Pod Autoscaling in Kubernetes. I successfully:

- Enabled the necessary Metrics Server.

- Configured an application with resource requests.

- Created an HPA to automatically scale based on CPU utilization.

- Simulated load to trigger the autoscaling.

- Observed the increase in pod replicas as the CPU target was met.

HPA is a cornerstone for building scalable and resilient applications on Kubernetes, adapting to demand fluctuations and optimizing resource usage.