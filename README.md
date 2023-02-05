# micro-service-docker-kubernetes
A repository to deploy the micro-service application on kubernetes cluster

## Create a file containing the deployment configuration, lets say `app-deploy.yml`
1. Deployment
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dept-api
spec:
  selector:
    matchLabels:
      app: dept-api
  replicas: 1
  template:
    metadata:
      labels:
        app: dept-api
    spec:
      containers:
      - name: dept-api
        image: ecominds/dept-service
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 8081
```

2. Service
```
apiVersion: v1
kind: Service
metadata:
  name: dept-api-service
spec:
  selector:
    app: dept-api
  ports:
  - protocol: "TCP"
    port: 8081
    targetPort: 8081
  type: LoadBalancer
```

## Deploy the application with the following command
```
$ kubectl create -f app-deploy.yml
```
**Note:** After the app is deployed, it might take the load balancer a few seconds to load.

*While waiting for the load balancer to deploy, we can check the status of the cluster with these commands:*
**Get each pods status:** `kubectl get pods`
**Get app status:** `kubectl get deployment`

## Check if the load balancer is live
```
$ kubectl get services
```
#Output:
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
dept-api-service   LoadBalancer   10.100.246.121   localhost     8081:31931/TCP   10m
kubernetes         ClusterIP      10.96.0.1        <none>        443/TCP          75m

## Use the load balancer IP address to access the app in a browser:
```
http://<load-balancer-IP-address>:8081/api/departments/
```

## Undeploy the application from the cluster. (Optional) To remove application, execute the below command:
```
$ kubectl delete -f app-deploy.yml
```
#Output:
deployment.apps/python-hello-app deleted
service "python-hello-app-lb" deleted
