# Konecta-K8s
Konecta-K8s tasks.

'create pod nginx with name my nginx direct from command don't use yaml file'
Explanation: We use the kubectl run command to create a pod named my-nginx directly from the command line. This command uses the default nginx image available on Docker Hub.
Command used: kubectl run my-nginx --image=nginx

'create pod nginx with name my nginx command and use Image nginx123 direct from command don't use yaml file'
Explanation: In this step, we attempt to create a pod with the name (my-nginx) but specify a different image, nginx123.
Command used: kubectl run my-nginx --image=nginx123

'check the status and why it dosn't work'
Explanation: After creating the pod, it’s important to check its status to determine if it’s running as expected or if there are errors. 
The second pod could not be created because we cannot create 2 pods with the same name.
Command used: kubectl get pods , kubectl describe pod my-nginx

'I need to know node name - IP - Image'
Explanation: To gather more detailed information about the pod, such as the node on which it is running, the pod’s IP address, and the image being used, we run the following command. The -o wide option provides extended output details that include node name and pod IP.
Command used: kubectl get pod my-nginx -n default -o wide , this command gets the Node Name and Pod IP
kubectl describe pod my-nginx -n default , this command gets the container image 

'delete the pod'
Explanation: Once you have finished inspecting and testing the pod, it’s good practice to clean up your environment by deleting the pod.
Command used: kubectl delete pod my-nginx

'create another one with yaml file and use label'
Explanation: In this task, we create a pod defined by a YAML file instead of using the direct command. The YAML file includes metadata labels so you can easily select or filter the pod later. 
Command used: kubectl apply -f my-nginx.yaml

'create Riplicaset with 3 riplcas using nginx Image'
Explanation: A ReplicaSet ensures that a specified number of pod replicas are running at any given time. Here, we define a ReplicaSet in a YAML file with 3 replicas using the nginx image. The selector (matchLabels) and pod template labels must match, ensuring that the ReplicaSet controls the pods it creates.
Command used: kubectl apply -f nginx-replicaset.yaml

'scale the replicas to 5 without edit in the Yaml file'
Explanation: Instead of modifying the YAML file, we use the kubectl scale command to adjust the number of replicas. This command updates the ReplicaSet’s desired state from 3 to 5, and Kubernetes will create additional pods accordingly.
Command used: kubectl scale replicaset nginx-replicaset --replicas=5

'Delete any one of the 5 pods and check what happen and explain'
Explanation: When you manually delete one pod from a ReplicaSet, Kubernetes’ controller detects that the number of running pods is less than the desired count (which is set to 5). It automatically creates a new pod to replace the one that was deleted, ensuring that the total number of pods remains consistent with the ReplicaSet specification.
Command used: kubectl get pods -l app=nginx , lists pods
kubectl delete pod <pod-name> , deletes pod
kubectl get pods -l app=nginx , Check the ReplicaSet status

'Scale dwon the pods again to 2 without scale command use terminal'
Explanation: To scale down without using the kubectl scale command, you can manually edit the ReplicaSet’s configuration directly from the terminal. The kubectl edit command opens the ReplicaSet’s configuration in your default text editor. You then change the replicas field from 5 to 2. Once you save and exit, Kubernetes updates the ReplicaSet to run only 2 pods, and it will terminate extra pods to meet the new desired count.
Command used: kubectl edit replicaset nginx-replicaset
kubectl get replicaset nginx-replicaset , kubectl get pods -l app=nginx , these commands verify the new replica count

11- find out the issue in the below Yaml (don't use AI)
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-2
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: nginx
    spec:
      containers:
      - name: nginx
        image: nginx

The issue is that the ReplicaSet Selector and Pod Template Labels are not matching.
The ReplicaSet expects to manage pods that have the label tier: frontend, however, the pod template in the YAML is setting a different label: tier: nginx

12- find out the issue in the below Yaml (don't use AI)

apiVersion: apps/v1
kind: deployment
metadata:
  name: deployment-1
spec:
  replicas: 2
  selector:
    matchLabels:
      name: busybox-pod
  template:
    metadata:
      labels:
        name: busybox-pod
    spec:
      containers:
      - name: busybox-container
        image: busybox
        command:
        - sh
        - "-c"
        - echo Hello Kubernetes! && sleep 3600

In Kubernetes, the kind field is case-sensitive. The standard and correct value for a deployment is Deployment with a capital "D".

In Kubernetes, a Deployment resource is part of the apps API group. This means its API version should be set to apps/v1 rather than v1. 

'what's command you use to know what Image name that running the deployment'
Command used: kubectl get deployment deployment-1 -o jsonpath='{.spec.template.spec.containers[*].image}'
Explanation: kubectl get deployment deployment-1: This command retrieves the details of the deployment named deployment-1.
-o jsonpath='{.spec.template.spec.containers[*].image}': This output option uses a JSONPath expression to filter and display only the image field for all containers in the deployment's pod template.

'create deployment using followiing data : Name: httpd-frontend; Replicas: 3; Image: httpd:2.4-alpine'
Explanation: We will create a deployment using the specified name and image. 
Command used: kubectl apply -f httpd-frontend-deployment.yaml

'replace the image to nginx777 with command directly'
Explanation: To update the image of an existing container in the deployment without modifying the YAML file, we use the kubectl set image command. 
Command used: kubectl set image deployment/httpd-frontend httpd-frontend=nginx777

'rollback to pervious version'
Explanation: Kubernetes deployments keep a revision history of changes. To revert the deployment to its previous state (before the image update), we use the kubectl rollout undo command. This command will rollback the deployment to the last successful revision.
Command used: kubectl rollout undo deployment/httpd-frontend

'Create a Simple Web Application: Use a Dockerfile to create a simple web application (e.g., an Nginx server serving an HTML page). , Build the Docker image and push it to DockerHub.'
Explanation: Create a file named Dockerfile with the following content. This file uses the official Nginx image (based on Alpine Linux), copies a simple HTML page into the container, and exposes port 80. Create an index.html file in the same directory.
Command used: docker build -t <your-dockerhub-username>/simple-nginx:latest .
docker push <your-dockerhub-username>/simple-nginx:latest

'Create a Deployment Using This Image: Deploy the Docker image from DockerHub to Kubernetes with a Deployment that has 3 replicas.'
Explanation: Create a deployment YAML file that deploys the image from DockerHub with 3 replicas.
Command used: kubectl apply -f frontend-deployment.yaml

'Expose the Frontend (FE) Using a NodePort Service: Expose the deployment with a NodePort service to make it accessible from your browser.'
Explanation: Create a NodePort service YAML file to make the deployment accessible from your browser.
Command used: kubectl apply -f frontend-service.yaml

'Create a Backend (BE) Deployment: Create another Deployment for the backend using the following data: Image: python:3.8-slim , Command: ["python", "-m", "http.server", "8080"] (include this command in the deployment file).'
Explanation: Create a file called backend-deployment.yaml , apply it , Check if it’s running
Command used: kubectl apply -f backend-deployment.yaml , kubectl get pods -o wide

'22- Expose the Backend Using a ClusterIP Service:
* Create a ClusterIP service to expose the backend deployment internally within the cluster.
* Use kubectl command to make it accessible from your browser.'
Create backend-service.yaml , Apply it , verify
Since ClusterIP is internal, we need to test it from within the cluster: Open a debug pod: kubectl run test-pod --image=alpine --restart=Never -- sleep 3600
Access the backend from inside: kubectl exec -it test-pod -- wget -qO- http://backend-service:8080

'Create a LoadBalancer Service:
* Create a LoadBalancer service for your frontend.
* Explain what happens when you try to apply it in an environment that does not support load balancers (e.g., Minikube).'
Create frontend-loadbalancer.yaml 
Apply: kubectl apply -f frontend-loadbalancer.yaml
Verify: kubectl get svc frontend-loadbalancer
On Minikube or Unsupported Environments: LoadBalancer services will not work because there is no external load balancer available. Instead, Minikube provides a workaround: minikube tunnel

'Explain DaemonSet and Provide a YAML File:
* Explain what a DaemonSet is and how it works.
* Provide a YAML file to create a DaemonSet in Kubernetes.'
A DaemonSet ensures that a copy of a pod runs on every node in the cluster.
It is useful for monitoring agents, logging daemons, and network components that need to run on all nodes.
Create daemonset.yaml
Apply: kubectl apply -f daemonset.yaml
Check: kubectl get daemonset


Part2:
Kubernetes Concepts Overview
1. Namespace
A **Namespace** in Kubernetes is a way to logically divide cluster resources. It helps in organizing and isolating workloads within the same cluster.  

**Use Cases:**  
- Multi-tenancy: Different teams can use separate namespaces.  
- Environment separation: `dev`, `staging`, and `prod` namespaces.  

**Commands:**  
- List namespaces:  
  kubectl get namespaces
  ```  
- Create a namespace:  
  kubectl create namespace my-namespace
  ```  
- Run a pod inside a namespace:  
  kubectl run nginx --image=nginx --namespace=my-namespace
  ```  
- Delete a namespace:  
  kubectl delete namespace my-namespace
  ```  
---

2. ConfigMap 
A **ConfigMap** is used to store non-sensitive configuration data as key-value pairs.  

**Use Cases:**  
- Store environment variables.  
- Store configuration files and pass them to pods.  

**Commands:**  
- Create a ConfigMap from a file:  
  kubectl create configmap my-config --from-file=config.properties
  ```  
- Create a ConfigMap from literals:  
  kubectl create configmap my-config --from-literal=APP_ENV=production --from-literal=LOG_LEVEL=info
  ```  
- Get ConfigMap details:  
  kubectl get configmap my-config -o yaml
  ```  
---

**3. Secret**  
A **Secret** is used to store sensitive information like passwords, API keys, or TLS certificates securely.  

**Use Cases:**  
- Store database credentials.  
- Store API tokens.  

**Commands:**  
- Create a Secret from literals:  
  kubectl create secret generic my-secret --from-literal=username=admin --from-literal=password=supersecret
  ```  
- Decode a Secret:  
  echo "dXNlcm5hbWU=" | base64 --decode
  ```  
- Get Secret details:  
  kubectl get secret my-secret -o yaml
  ```  

---

**4. Network Policy**  
A **NetworkPolicy** controls how pods communicate with each other and with other network endpoints.  

**Use Cases:**  
- Restrict traffic between namespaces.  
- Allow only specific applications to communicate.  

**Commands:**  
- Get Network Policies:  
  kubectl get networkpolicy
  ```  
- Apply a Network Policy:  
  kubectl apply -f network-policy.yaml
  ```  
- Describe a Network Policy:  
  kubectl describe networkpolicy allow-frontend
  ```  

---

**5. Taint and Toleration**  
**Taints** are applied to nodes to **restrict** which pods can be scheduled on them.  
**Tolerations** are applied to pods to **allow** them to run on tainted nodes.  

**Use Cases:**  
- Dedicate nodes for specific workloads.  
- Prevent workloads from running on critical nodes.  

**Commands:**  
- Apply a taint to a node:  
  kubectl taint nodes node1 key=value:NoSchedule
  ```  
- Remove a taint:  
  kubectl taint nodes node1 key=value:NoSchedule-
  ```  

**6. Volume**  
A **Volume** in Kubernetes is used to persist data across container restarts.  

**Types of Volumes:**  
- **EmptyDir**: Temporary storage that is deleted when the pod stops.  
- **HostPath**: Uses a directory from the node's filesystem.  
- **PersistentVolume (PV) & PersistentVolumeClaim (PVC)**: Storage that persists beyond pod lifetimes.  

**Commands:**  
- Create a PVC:  
  kubectl apply -f pvc.yaml
  ```  
- Check PVC status:  
  kubectl get pvc
  ```  









