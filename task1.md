
# Task 1 - Week 6: My Journey Through Kubernetes Controllers

The main goal here was to see how Kubernetes makes sure our apps are always running and can handle different loads. We looked at three ways to do this: ReplicationControllers (RCs), ReplicaSets (RSs), and Deployments.

## 1. Setting Up ReplicationController (RC) 

First, I created a ReplicationController using `replicationcontroller.yaml`. This is an older but still functional way to ensure a specified number of identical Pods are always running. I then used `kubectl apply` to create the ReplicationController and `kubectl get` to check its status. I also used `kubectl describe` to see more details about the RC. Finally, I scaled the RC up and down to see how it handled the changes.

```bash
kubectl apply -f replicationcontroller.yaml
```
![RC Creation](Images/imagestask1/Screenshot%202025-07-11%20121911.png)

The output `replicationcontroller/rc-nginx created` confirmed it was set up. Later, `kubectl get all` showed `rc-nginx` desired and current replicas were 2, meaning it was managing two Nginx pods (like rc-nginx-sxhsr and rc-nginx-wl6hf). 

### My Takeaway on RCs:

- **Purpose:** Keeps a fixed number of Pods running. If one dies, it'll create a new one. Simple!
- **Advantage:** Guarantees availability.
- **Disadvantage:** It's quite basic. It doesn't support advanced features like rolling updates or rollbacks directly. You'd have to manage those manually if you used just an RC. Think of it as a vigilant, but not very smart, babysitter for your pods. It ensures they're there, but doesn't help with much else. 

## 2. Struggling with ReplicaSet (RS) 

Next, I tried to apply `replicaset.yaml`, but hit a snag:

```bash
kubectl apply -f replicaset.yaml
```
*Shows error: the path "replicaset.yaml" does not exist.*

I then realized the correct file was `replica-set-01.yaml`. After fixing that, I successfully created a ReplicaSet:

```bash
kubectl apply -f replica-set-01.yaml
```
![ReplicaSet Apply](Images/imagestask1/Screenshot%202025-07-11%20122019.png)

The `replicaset.apps/rs-nginx created` message confirmed it.

### My Takeaway on RSs:

- **Purpose:** Newer generation of RCs. It's essentially the same core function: maintaining a stable set of replica Pods running at any given time. However, it's more flexible and powerful than RCs. It supports more features like labels and selectors, making it easier to manage and scale your Pods. Think of it as a more intelligent, capable babysitter for your pods. It not only ensures they 're there but also helps with more advanced tasks.
- **Advantage:** Supports more advanced set-based label selectors. This makes it easier to manage and scale your Pods. It also supports more features like rolling updates and rollbacks. 
- **Disadvantage:** While better than RCs for selecting pods, it still doesn't offer "smart" deployment strategies directly. It's more of a building block. You'd still need to manage those manually if you used just a ReplicaSet. Think of it as a more capable, but still somewhat limited, babysitter for your pods. It ensures they're there and helps with some advanced tasks, but doesn't do everything on its own. 

## 3. Deploying with Deployment (The Modern Way!) 

This was the main event! Deployments are the go-to for managing applications in Kubernetes because they use ReplicaSets under the hood and add powerful features like rolling updates and rollbacks. 

```bash
kubectl apply -f deployment.yaml
```
![Deployment Apply](Images/imagestask1/Screenshot%202025-07-11%20121911.png)

This created `deployment.apps/deploy-nginx`. Initially, `kubectl get all` showed `deploy-nginx` with `READY 1/2` and `AVAILABLE 1`. This means it was managing one Nginx pod (like deploy-nginx-7f6f 8f5f6f). I then scaled the deployment up and down to see how it handled the changes. Later, I used `kubectl rollout status` to check the status of the rollout. Finally, I used `kubectl rollout history` to see the history of the rollout. 

```bash
kubectl get all
```
![Get All After Deployment](Images/imagestask1/Screenshot%202025-07-11%20121928.png)

### Scaling the Deployment

```bash
kubectl scale deployment deploy-nginx --replicas=4
```
![Scaling](Images/imagestask1/Screenshot%202025-07-11%20122046.png)

```bash
kubectl get pods
```
![Pods After Scaling](Images/imagestask1/Screenshot%202025-07-11%20122104.png)

### My Takeaway on Deployments:

- **Purpose:** The highest-level object for managing applications.
- **Advantages:**
  - Automated Rolling Updates
  - Rollbacks
  - Self-healing
  - Declarative Updates
- **Disadvantages:** More complex than RCs and RSs, and not suited for stateful apps (that's where StatefulSets come in!). 

## Final Cluster State After RS Creation

```bash
kubectl get all
```
![Final State](Images/imagestask1/Screenshot%202025-07-11%20122033.png)

---

## Conclusion 

This task really highlighted the evolution of Kubernetes controllers. While ReplicationControllers and ReplicaSets handle basic pod replication, Deployments are the powerhouse. They offer robust features for managing the lifecycle of your applications, especially when it comes to updates and scaling, making them indispensable for production environments. Understanding these differences is key to building reliable and scalable applications on Kubernetes! 
