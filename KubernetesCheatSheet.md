# Part 1 Overview
### Minikube VM
minikube version
minikube start
### Kubernetes CLI
kubectl version
kubectl cluster-info
kubectl get nodes

# Part 2 Deployment
### Get help
kubectl get nodes --help
### Run app on a specific port
kubectl run kubernetes-bootcamp -image=gcr.io/google-samples/kubernetes-bootcamp:v1--port:8080
### List deployments
kubectl get deployments
### Open a proxy that forward communications into the cluster-wide, private network
kubectl proxy
### Query the version directly from the api endpoint hosted through the proxy endpoint
curl http://localhost:8001/version
### Get the pod name and store in an environment variable
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME
### Make an HTTP request to the application running on that pod
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/

# Part 3 Viewing Pods and Nodes
### Look for existing pods
kubectl get pods
### View what containers are running in the pods, what images are used to build them, IP address and ports used in the container, and events related to the lifecycle of the pod
kubectl describe pods
### View the container logs after getting the pod name
kubectl logs $POD_NAME
### Execute commands directly in the container when the pod is running
kubectl exec $POD_NAME env
### Start bash session in the pod's container
kubectl exec -ti $POD_NAME bash
### View the source code of the app in the bash
cat server.js
### Check if the application is up and running in the bash
curl http:localhost:8080
### Exit the bash session
exit

# Part 4 Exposing your app
### List current services from the cluster
kubectl get services
### Create a new service and expose to external traffic
kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
### Find out what port was opened externally by the NodePort
kubectl describe services/kubernetes-bootcamp
### Assign the NodePort to an environment variable
export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT
### Test if the app is exposed outside of the cluster
curl $(minikube ip):$NODE_PORT
### See the label automatically created for the pod after deployment
kubectl describe deployment
### Use the label to list the pods
kubectl get pods -l run=kubernetes-bootcamp
### List current services with pod label
kubectl get services -l run=kubernetes-bootcamp
### Apply a new label to the pod after exporting the pod name to the environment variable
kubectl label pod $POD_NAME app=v1
### Check the label of the pod
kubectl describe pods $POD_NAME
### Delete service using label
kubectl delete service -l run=kubernetes-bootcamp
### Curl inside the pod to check if the app is still running
kubectl exec -ti $POD_NAME localhost:8080

# Part 5 Scaling your app
### List deployments and see DESIRED (configured number of replicas), CURRENT (how many replicas are running now), UP-TO-DATE (number of replicas that were updated to match the desired state), AVAILABLE (how many replicas are currently available to the users)
kubectl get deployments
### Scale the Deployment to 4 replicas
kubectl scale deployments/kubernetes-bootcamp --replicas=4
### Check the number of pods
kubectl get pods -o wide
### Check the Deployment's events log
kubectl describe deployments/kubernetes-bootcamp
### Find out exposed IPs and ports
kubectl describe services/kubernetes-bootcamp
### Doing a curl on the exposed IP and port hits a different pod every time
curl $(minikube ip):$NODE_PORT
### Scale down to 2 replicas
kubectl scale deployments/kubernetes-bootcamp --replicas=2

# Part 6 Updating your app
### Update the image of an application
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
### Find out the IP and port of the service
kubectl describe services/kubernetes-bootcamp
### Confirm update
kubectl rollout status deployments/kubernetes-bootcamp
### Roll back to previously working version
kubectl rollout undo deployments/kubernetes-bootcamp

# Extras
### Pass config files into Kubernetes
kubectl create configmap docker-config --from-file=$HOME/.docker/config.json
### Create pod
kubectl apply -f pod-kaniko.yml
### Get pods and watch on it
kubectl get pods -w
### Get logs
kubectl logs kaniko
### Get component status
kubectl get cs
### Get replica set
kubectl get rs
### Get api endpoints to send commands to
kubectl api-versions

# Udemy
### Deploy nginx on kubernetes
kubectl run nginx --image=nginx:1.10.0
### Expose deployments
kubectl expose deployments nginx --port 80 --type LoadBalancer
### Create the pod from configuration file
kubectl create -f `file`.yaml
### Map a local port into the port inside the pod
kubectl port-forward `pod` 10080:80
### View stream of logs in the pod in real-time
kubectl logs -f `pod`
### Run an interactive shell inside the pod
kubectl exec `pod` --stdin --tty -c `pod` /bin/sh
### Create secret
kubectl create secret generic tls-certs --from-file=tls/


## For more info
https://kubernetes.io/docs/reference/kubectl/cheatsheet/#deleting-resources
