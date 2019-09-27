## intro

- You can list the active account name with this command:

gcloud auth list

- You can list the project ID with this command:

gcloud config list project

- Define your zone as a project default zone. This way you do not need to specify --zone parameter in gcloud commands.

gcloud config set compute/zone us-central1-a

- Get the sample code for creating and running containers and deployments:

git clone https://github.com/googlecodelabs/orchestrate-with-kubernetes.git

- Start your Kubernetes cluster with 5 nodes.

cd orchestrate-with-kubernetes/kubernetes

### Create cluster called bootcamp with 5 nodes

- In Cloud Shell, run the following command to start a Kubernetes cluster called bootcamp that runs 5 nodes.

gcloud container clusters create bootcamp --num-nodes 5 --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"

- The scopes argument provides access to project hosting and Google Cloud Storage APIs that you'll use later.
- It takes several minutes to create a cluster as Kubernetes Engine provisions virtual machines for you. It spins up one or more master nodes and multiple configured worker nodes. This is one of the advantages of a managed service.

## Learn about Deployment Objects

### Run the explain command in kubectl to tell you about the deployment object.

kubectl explain deployment

- Run the command with the --recursive option to see all of the fields.

kubectl explain deployment --recursive

- Use the explain command as you go through the lab to help you understand the structure of a deployment object and understand what the individual fields do.

kubectl explain deployment.metadata.name

### Create a Deployment

- Examine the deployment configuration file.

cat deployments/auth.yaml

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: auth
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: auth
        track: stable
    spec:
      containers:
        - name: auth
          image: "kelseyhightower/auth:2.0.0"
          ports:
            - name: http
              containerPort: 80
            - name: health
              containerPort: 81
          resources:
            limits:
              cpu: 0.2
              memory: "10Mi"
          livenessProbe:
            httpGet:
              path: /healthz
              port: 81
              scheme: HTTP
            initialDelaySeconds: 5
            periodSeconds: 15
            timeoutSeconds: 5
          readinessProbe:
            httpGet:
              path: /readiness
              port: 81
              scheme: HTTP
            initialDelaySeconds: 5
            timeoutSeconds: 1
```

- kubectl create will create the auth deployment with one replica, using version 1.0.0 of the auth container. To scale the number of pods, you simply change the replicas field.

- Create the deployment object using kubectl create.

kubectl create -f deployments/auth.yaml

- Verify that it was created.

kubectl get deployments

- Kubernetes creates a ReplicaSet for the deployment.

Run the following command to verify it. You should see a ReplicaSet with a name like auth-xxxxxxx.

kubectl get replicasets

- Run the following command to view the pods created for your deployment. A single pod was created when the ReplicaSet was created.

kubectl get pods

- With your pod running, it's time to put it behind a service. Use the kubectl create command to create the auth service.

kubectl create -f services/auth.yaml

- Do the same to create and expose the hello and frontend deployments.
- 
```
kubectl create -f deployments/hello.yaml

kubectl create -f services/hello.yaml


kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf

kubectl create secret generic tls-certs --from-file tls/

kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml
```

- You created a ConfigMap and secret for the frontend.

- Interact with the frontend.

Get its external IP.

kubectl get services frontend

- And curl the service.

curl -ks https://<EXTERNAL-IP>

- You get the "hello" response. Use the output templating feature of kubectl to run curl as a one-line command.

curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`

## Scale a Deployment

- Update the spec.replicas field to scale the deployment.

- Run the kubectl explain command to see an explanation of the field.

kubectl explain deployment.spec.replicas

- You can update the replicas field most easily using the kubectl scale command.

kubectl scale deployment hello --replicas=5

- Kubernetes updates the ReplicaSet and starts new pods to equal 5.

Verify there are 5 pods running.

kubectl get pods | grep hello- | wc -l

- Scale back the application.

kubectl scale deployment hello --replicas=3

- Verify the correct number of pods.

kubectl get pods | grep hello- | wc -l

## Rolling updates

- Deployments update images to new versions through rolling updates. When a deployment is updated with a new version, it creates a new ReplicaSet and slowly increases the number of replicas in the new ReplicaSet as it decreases the replicas in the old ReplicaSet.

- Run the following command to update your deployment.

kubectl edit deployment hello

- Change the image in containers section to the following, then save and exit.

- The updated deployment is saved to your cluster and Kubernetes begins a rolling update.

- You can see the new ReplicaSet that Kubernetes creates.

kubectl get replicaset

- If you fail to see a new ReplicaSet, make sure you changed the image in containers, and not one of the other references in labels.

- View the new entry in the rollout history.

kubectl rollout history deployment/hello

## Pause a Rolling update

- Pause the update.

kubectl rollout pause deployment/hello

- Verify the current state of the rollout.

kubectl rollout status deployment/hello

- Verify this with the pods.

kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'

## Resuming a Rolling Update

- The rollout is paused which means that some pods are at the new version and some pods are at the older version.

- Use the resume command to continue the rollout.

kubectl rollout resume deployment/hello

- Run the status command to verify the rollout is complete.

kubectl rollout status deployment/hello

## Rollback an Update

- If a bug occurs in your new version, users connected to new pods will experience the issue.

- Use the rollout undo command to roll back to the previous version, then fix any bugs.

kubectl rollout undo deployment/hello

- Verify the rollback in the deployment's history.

kubectl rollout history deployment/hello

- Verify all pods have rolled back to the previous version.

kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'

## Canary Deployments

- Run a canary deployment to test a new deployment in production with a subset of users. This mitigates risk with new releases.

- Create a Canary Deployment

A canary deployment consists of a separate deployment from your stable deployment and a service that targets them both at the same time.

- Examine the file that creates a canary deployment for your new version.

cat deployments/hello-canary.yaml

- It includes the following:
```
the deployment hello-canary
1 pod (replica)
selectors app: hello and track: canary
an image with version 2.0.0.
```

- Create the canary deployment.

kubectl create -f deployments/hello-canary.yaml

- After the canary deployment is created, verify you have two deployments hello and hello-canary.

kubectl get deployments

- The hello service selector uses app: hello, which matches pods in both deployments. However, the canary deployment has fewer pods, and is only used by a subset of users.

- You can verify both hello versions being served by requests.

curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version

- Run the command several times and confirm that hello 1.0.0 serves about ¾ (75%) of requests and 2.0.0 serves about ¼ (25%).

### Session affinity for Canary Deployments to give users the same kind of version every time
```
By default, every request has a chance to be served by the canary deployment. If you want users to get all their responses from the same version, enable session affinity in the configuration file as follows:

spec:

sessionAffinity: ClientIP
```

- Delete it and the service as follows.

kubectl delete deployment hello-canary

## Blue-Green Deployments

- You can use blue-green deployments if it's more beneficial to modify load balancers to point to a new, fully-tested deployment all at once.

- A downside is you need double the resources to host both versions of your application during the switch.

### The service

- You use the existing hello deployment for the blue version and a new hello-green deployment for the green version.
- You use two nearly-identical service files (hello-blue and hello-green) to switch between versions. The only difference between these files is their version selector. You could edit the service while it's running and change the version selector, but switching files is easier for labs.

-First, update the service to use the blue deployment:

kubectl apply -f services/hello-blue.yaml

```
kind: Service
apiVersion: v1
metadata:
  name: "hello"
spec:
  selector:
    app: "hello"
    version: 1.0.0
  ports:
    - protocol: "TCP"
      port: 80
      targetPort: 80
```


### Create a blue-green deployment

- Create the green deployment.

kubectl create -f deployments/hello-green.yaml

- Verify the blue deployment (1.0.0) is still being used.

curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version

- Run the following command to update the service to use the green deployment.

kubectl apply -f services/hello-green.yaml

- Verify the green deployment is being used.

curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version

### Roll-back a blue-green deployment

- While the green deployment is still running, simply update the service to the old (blue) deployment.

kubectl apply -f services/hello-blue.yaml

- Verify that the blue deployment is being used.

curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version

## Use the Web UI

- You can also use Admin Console to view and interact with your Kubernetes Engine objects in the web interface rather than the command-line.

- In Admin Console, click Navigation menu and select Kubernetes Engine.

Select a resource in the tab on the left, for example Services.

- Investigate each set of objects by navigating through the following pages:

```
Clusters
Workloads (Pods)
Services
Configuration (ConfigMaps and Secrets).
```

- On each page, scroll over an item in the Name field and click its link to view more information about it. For example, on the Services page, click frontend. Your front end Services will display as follows with status, types, endpoints, and number of running Pods: