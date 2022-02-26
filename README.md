## Description
PoC deploy a simple nodeJS service in minikube

# Steps to follow
- Create and Tag a service docker image from Dockerfile
```shell
docker build -t masalinasgancedo/my-node .
```

- Publish image in Docker Hub account
```shell
docker push masalinasgancedo/my-node
```

- Create kubernetes **sample** namespace
```shell
kubectl apply -f namespace-my-node.yaml
```

- Only In the case that the image registry where publish our image was private

Create Docker Hub credentials to pull docker hubs docker images
```shell
kubectl create secret docker-registry my-dockerhub-credetials -n sample --docker-username=<DOCKER_HUB_USERNAME> --docker-password=<DOCKER_HUB_PASSWORD> --docker-email=<DOCKER_HUB_EMAIL>
```

Add **imagePullSecrets** attribute inside deployment artifact like this:
```shell
    spec:
      containers:
      - name: my-node
        image: masalinasgancedo/my-node
        ports:
        - containerPort: 8080
      imagePullSecrets:
      - name: my-dockerhub-credetials
```

- Create deployment in minikube
```shell
kubectl apply -f deployment-my-node.yaml
```

- Expose deployment like NodePort service in minikube, create a port forward and load service from browser
```shell
kubectl expose deployment -n sample my-node-deployment --name=my-node-service --type=NodePort --port=8080
```

```shell
kubectl port-forward -n sample service/my-node-service 8080:8080
```

```shell
http://localhost:8080
```

- Expose deployment like LoadBalancer service in minikube, create a tunnel and load service from browser
```shell
kubectl expose deployment -n sample my-node-deployment --name=my-node-service --type=LoadBalancer --port=8080
```

```shell
minikube tunnel --cleanup
```

```shell
http://10.96.33.203:8080
```
