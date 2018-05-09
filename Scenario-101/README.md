Table of Contents
=================
<a href="https://www.dennyzhang.com"><img align="right" width="201" height="268" src="https://raw.githubusercontent.com/USDevOps/mywechat-slack-group/master/images/denny_201706.png"></a>

   * [Requirements](#requirements)
   * [Background &amp; Highlights](#background--highlights)
   * [Procedures](#procedures)
      * [Deployment](#deployment)
      * [Verify Deployment](#verify-deployment)
   * [More resources](#more-resources)

# Requirements
<a href="https://www.dennyzhang.com"><img align="right" width="185" height="37" src="https://raw.githubusercontent.com/USDevOps/mywechat-slack-group/master/images/dns_small.png"></a>

Scenario-101: 1-Node K8S Deployment I
- Objective: Get familiar with K8S concept. Here K8S stands for Kubernetes.
- Requirements:
```
1. Start one node of k8s in your laptop. Mac or Linux
2. Start a nginx webserver with one instance
3. Scale nginx service into 2 instances.
4. Get familiar with k8s dashboard. Find pods from GUI, check nginx log.
```

See [kubernetes.yaml](kubernetes.yaml)

# Background & Highlights
- Q: How to use your own docker image?

- Q: What does "kubectl expose deployment .." do behind the scene?

# Procedures

For single node deployment, we have [multiple choices](https://kubernetes.io/docs/setup/pick-right-solution/#local-machine-solutions). Here we use [minikube](https://kubernetes.io/docs/getting-started-guides/minikube/).

## Deployment
- Install virtualbox and minikube
```
which minikube
which kubectl
```

- Start env
```
# start a VM to host our deployment
minikube start

# Create k8s deployment and export service
kubectl create -f ./kubernetes.yaml
```
See [kubernetes.yaml](kubernetes.yaml)

## Verify Deployment
- Check k8s web UI Dashboard
```
minikube dashboard
```

- List k8s resources
```
# list deployments
kubectl get deployment

# list service
kubectl get services

# list pods
kubectl get pods
```

- Check the first pod
```
# Get all pods
kubectl get pods -l app="nginx"

# Get the first pod
POD_NAME=$(kubectl get pods -l app="nginx" -o jsonpath="{.items[0].metadata.name}")
echo "POD_NAME: $POD_NAME"

# Login to the first pod
kubectl exec -ti $POD_NAME hostname

# Check log of the first pd
kubectl logs -f $POD_NAME
```

- Run functional test
```
# Get port nginx service
lzhshen@k8s-test-203101:~/challenges-kubernetes/Scenario-101$ kubectl get service
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP   10.47.240.1     <none>        443/TCP        5d
my-nginx-service   NodePort    10.47.242.202   <none>        80:31817/TCP   25m

# Add firefox rules for the port
gcloud compute firewall-rules create allow-101-nginx-nodeport --allow=tcp:31817

# Get external IP
lzhshen@k8s-test-203101:~/challenges-kubernetes/Scenario-101$ gcloud compute instances list
NAME                               ZONE               MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
gke-k0-default-pool-000b8a1f-1n8k  asia-southeast1-a  n1-standard-1               10.148.0.2   35.198.240.69  RUNNING
gke-k0-default-pool-000b8a1f-5nwr  asia-southeast1-a  n1-standard-1               10.148.0.4   35.198.221.59  RUNNING
gke-k0-default-pool-000b8a1f-6950  asia-southeast1-a  n1-standard-1               10.148.0.3   35.198.206.72  RUNNING

# In browser, input http://35.198.240.69:31817 and then nginx welcome page should be shown

```

Destroy env:
- Delete k8s resources
```
kubectl delete -f ./kubernetes.yaml
```

- Remove VM
```
minikube delete
```

# More resources
- minikube: https://kubernetes.io/docs/getting-started-guides/minikube/
- k8s local solutions: https://kubernetes.io/docs/setup/pick-right-solution/#local-machine-solutions

<a href="https://www.dennyzhang.com"><img align="right" width="185" height="37" src="https://raw.githubusercontent.com/USDevOps/mywechat-slack-group/master/images/dns_small.png"></a>
