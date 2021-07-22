# netshoot-and-fake-api
An example of how two pods within a k8s namespace make direct calls to each other.

## Step 1: Create a namespace on your cluster
```
kubectl create namespace my-namespace
```

## Step 2: Deploy your faker (fake web api) pod.
This is a small mock web api that simply returns the json in the mocks folder. Courtesy of this [article](https://medium.com/@dotronglong/set-up-fake-api-in-minutes-with-docker-dfffebe264b0).


Build your docker image so that the mocks folder will also be within the container when it starts:
```
docker build . -t my-faker
```
Deploy it to your cluster:
```
kubectl apply -f faker.yaml -n=my-namespace

```
If you want to test your faker from your host then you may port forward the pod:
```
kubectl port-forward my-faker 3030:3030
```
## Step 3: Get the my-faker pod's ip
You will see it listed as the value to the IP key when you run:
```
kubectl describe pod my-faker
```

## Step 4: Run a curl command from within the netshoot pod
[nicolaka/netshoot](https://github.com/nicolaka/netshoot) is an awesome tool to troubleshoot your cluster network. We will simply use it to curl the faker pod.
First run the container:
```
kubectl run tmp-shell --rm -i --tty --image nicolaka/netshoot -n=my-namespace -- /bin/bash
```
Note the "/bin/bash" is the executable on the host machine. So for windows I just replaced it with "bash".

Now curl your faker:
```
curl http://<my-faker ip>:3030/v1/users
```

Daarsy!
