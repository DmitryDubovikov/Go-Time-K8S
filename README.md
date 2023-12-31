# Go-Time-K8S

## Run locally

Build docker image:

```bash
docker build . -t go-time

docker run -d -p 8080:8080 --name=go-time go-time
```
Check current time:

http://localhost:8080/time

## Run in minikube

Load docker image to minikube and check it:
```bash
minikube image load go-time

minikube image ls | grep go-time:latest
```

Start and expose deployment:
```bash
kubectl apply -f go-time-deployment.yaml

kubectl expose deployment go-time-deployment --type=LoadBalancer --port=8080
```

(Command for generating yaml:)
```bash
kubectl expose deployment go-time-deployment --type=LoadBalancer --port=8080 --dry-run=client -o yaml > go-time-service.yaml
```


Run the minikube tunnel command. This command will create a virtual network interface that provides access to LoadBalancer-type services from outside Minikube.
```bash
minikube tunnel
```

After running minikube tunnel, go back to your service and check the EXTERNAL-IP value:
```bash
kubectl get services
```
Now, the EXTERNAL-IP value should be populated and accessible from outside Minikube. You will be able to access your service through this external IP address.

```bash
dima@beelink:~/projects/Go-Time-K8S$ kubectl get services
NAME                 TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
go-time-deployment   LoadBalancer   10.104.145.121   127.0.0.1     8080:30889/TCP   2m29s
```
Check time using EXTERNAL-IP:

http://127.0.0.1:8080/time

## Run in cloud kubernetes

### Push the Docker Image to Docker Hub

Authenticate with Docker Hub:
```bash
docker login
```

Create a Docker Image:
```bash
docker build -t dubovikov/go-time:latest .
```

Push the Docker Image to Docker Hub:
```bash
docker push dubovikov/go-time:latest
```

Verify the Upload:
https://hub.docker.com/

### Create deployment

```bash
 kubectl apply -f go-time-deployment.yaml
```

### Expose created deployment

#### Service (LoadBalancer):

Service for internal load balancing and access to pods within the cluster.

```bash
kubectl expose deployment time-deployment --type=LoadBalancer --port=8080 --dry-run=client -o yaml > go-time-service.yaml

kubectl apply -f .\go-time-service.yaml
```

Let's scale deployment:
```bash
kubectl scale deployment time-deployment --replicas=3
```

#### Ingress

Ingress for managing external access and routing of HTTP traffic.



------------
```bash
dima@beelink:~/.kube$ ls
cache  config  kubeconfig-beeline.yaml
dima@beelink:~/.kube$ export KUBECONFIG=./kubeconfig-beeline.yaml
dima@beelink:~/.kube$ kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
time-deployment-75f69dd776-528s6   1/1     Running   0          110m
time-deployment-75f69dd776-8h654   1/1     Running   0          110m
time-deployment-75f69dd776-8wn8g   1/1     Running   0          87m

```