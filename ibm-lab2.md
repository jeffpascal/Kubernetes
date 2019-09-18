# Lab 2: Scale and update apps: Services, replica sets, and health checks current chapter

## Scale apps with replicas

```
A replica is how Kubernetes accomplishes scaling out a deployment. A replica is a copy of a pod that already contains a running service. By having multiple replicas of a pod, you can ensure your deployment has the available resources to handle increasing load on your app.

Update the replica set:

kubectl edit deployment/<name-of-deployment>

Edit the YAML file in the code editor of your choice.

This configuration YAML file is the configuration of your current deployment, which you can edit to customize the configuration for more fault tolerance.

You should see a configuration similar to the this:

    ...
    spec:
      replicas: 1
      selector:
        matchLabels:
          run: hello-world
      strategy:
        rollingUpdate:
          maxSurge: 1
          maxUnavailable: 1
        type: RollingUpdate
      template:
        metadata:
          creationTimestamp: null
          labels:
            run: hello-world
    ...
Change the replicas number from 1 to 10 so that the configuration now reads:

    ...
    spec:
      replicas: 10
      selector:
        matchLabels:
          run: hello-world
      strategy:
      rollingUpdate:
          maxSurge: 1
          maxUnavailable: 1
        type: RollingUpdate
      template:
        metadata:
          creationTimestamp: null
          labels:
            run: hello-world
    ...
Save your changes and exit the editor. You now have 10 replicas of the app, which provides fault tolerance.
Watch your changes being rolled out:

kubectl rollout status deployment/<name-of-deployment>

The rollout might occur so quickly that the following messages might not display:

    => kubectl rollout status deployment/hello-world
    Waiting for rollout to finish: 1 of 10 updated replicas are available...
    Waiting for rollout to finish: 2 of 10 updated replicas are available...
    Waiting for rollout to finish: 3 of 10 updated replicas are available...
    Waiting for rollout to finish: 4 of 10 updated replicas are available...
    Waiting for rollout to finish: 5 of 10 updated replicas are available...
    Waiting for rollout to finish: 6 of 10 updated replicas are available...
    Waiting for rollout to finish: 7 of 10 updated replicas are available...
    Waiting for rollout to finish: 8 of 10 updated replicas are available...
    Waiting for rollout to finish: 9 of 10 updated replicas are available...
    deployment "hello-world" successfully rolled out
After the rollout is finished, ensure your pods are running:

kubectl get pods

You should see output listing the 10 pods of your deployment:

    => kubectl get pods
    NAME                          READY     STATUS    RESTARTS   AGE
    hello-world-562211614-1tqm7   1/1       Running   0          1d
    hello-world-562211614-1zqn4   1/1       Running   0          2m
    hello-world-562211614-5htdz   1/1       Running   0          2m
    hello-world-562211614-6h04h   1/1       Running   0          2m
    hello-world-562211614-ds9hb   1/1       Running   0          2m
    hello-world-562211614-nb5qp   1/1       Running   0          2m
    hello-world-562211614-vtfp2   1/1       Running   0          2m
    hello-world-562211614-vz5qw   1/1       Running   0          2m
    hello-world-562211614-zksw3   1/1       Running   0          2m
    hello-world-562211614-zsp0j   1/1       Running   0          2m
Tip: Another way to improve availability is to add clusters and regions to your deployment as shown in the following diagram:
```

## Update and roll back apps

```
Kubernetes allows you to use a rollout to update an app deployment with a new Docker image. This allows you to easily update the running image and also allows you to easily undo a rollout if a problem is discovered after deployment.

Before you begin, ensure that you have the image tagged with 1 and pushed:

docker build --tag us.icr.io/<namespace>/hello-world:1 .

docker push us.icr.io/<namespace>/hello-world:1
To update and roll back:

Make a change to your code and build a new docker image with a new tag:

docker build --tag us.icr.io/<namespace>/hello-world:2 .

Push the image to the IBM Cloud Container Registry:

docker push us.icr.io/<namespace>/hello-world:2

Using kubectl, update your deployment to use the latest image. You can do this in two ways:
Edit the YAML file again by using kubectl edit deployment/<name-of-deployment>
Specify a new image by using a single command. Using a single command is especially useful when writing deployment automation. To specify the new image, run this command:
kubectl set image deployment/hello-world hello-world=us.icr.io/<namespace>/hello-world:2

A deployment can have multiple containers in which case each container will have its own name. Multiple containers can be updated at the same time. For more information about setting images, see the kubectl reference documentation. https://kubernetes.io/docs/user-guide/kubectl/kubectl_set_image/

Check he status of the rollout by running one of these commands:
kubectl rollout status deployment/<name-of-deployment>
kubectl get replicasets
The rollout might occur so quickly that the following messages might not be displayed:

    => kubectl rollout status deployment/hello-world
    Waiting for rollout to finish: 2 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 3 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 3 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 3 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 4 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 4 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 4 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 4 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 4 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 5 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 5 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 5 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 6 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 6 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 6 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 7 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 7 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 7 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 7 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 8 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 8 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 8 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 8 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 9 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 9 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 9 out of 10 new replicas have been updated...
    Waiting for rollout to finish: 1 old replicas are pending termination...
    Waiting for rollout to finish: 1 old replicas are pending termination...
    Waiting for rollout to finish: 1 old replicas are pending termination...
    Waiting for rollout to finish: 9 of 10 updated replicas are available...
    Waiting for rollout to finish: 9 of 10 updated replicas are available...
    Waiting for rollout to finish: 9 of 10 updated replicas are available...
    deployment "hello-world" successfully rolled out
    => kubectl get replicasets
    NAME                   DESIRED   CURRENT   READY     AGE
    hello-world-1663871401   9         9         9         1h
    hello-world-3254495675   2         2         0         <invalid>
    => kubectl get replicasets
    NAME                   DESIRED   CURRENT   READY     AGE
    hello-world-1663871401   7         7         7         1h
    hello-world-3254495675   4         4         2         <invalid>
    ...
    => kubectl get replicasets
    NAME                   DESIRED   CURRENT   READY     AGE
    hello-world-1663871401   0         0         0         1h
    hello-world-3254495675   10        10        10        1m
Confirm that your new code is active:

curl <public-IP>:<nodeport>

Optional: Undo your latest rollout:
kubectl rollout undo deployment/<name-of-deployment>
```

## Check the health of apps

``
Kubernetes uses availability checks (liveness probes) to know when to restart a container. For example, liveness probes can catch a deadlock where an app is running but be unable to make progress. Restarting a container in such a state can help to make the app more available despite the bugs.

Also, Kubernetes uses readiness checks to know when a container is ready to start accepting traffic. A pod is considered ready when all of its containers are ready. One use of this check is to control which pods are used as back ends for services. When a pod is not ready, it is removed from the load balancers.

In this example, an HTTP liveness probe is defined to check health of the container every five seconds. For the first 10 - 15 seconds, the /healthz returns a 200 response and will fail afterward. Kubernetes will automatically restart the service.

Open the <username_home_directory>/container-service-getting-started-wt/Lab 2/healthcheck.yml file with a text editor. This configuration script combines a few steps from the previous lesson to create a deployment and a service at the same time. App developers can use these scripts when updates are made or to troubleshoot issues by re-creating the pods. Make these changes in the file:
Update the details for the image in your private registry namespace:
image: "us.icr.io/<namespace>/hello-world:2"
Note the HTTP liveness probe that checks the health of the container every five seconds:
        livenessProbe:
                    httpGet:
                      path: /healthz
                      port: 8080
                    initialDelaySeconds: 5
                    periodSeconds: 5
In the Service section, note the NodePort. Rather than generating a random NodePort like you did previously, you can specify a port between 30000 – 32767. This example uses port 30072.
Run the configuration script in the cluster. When the deployment and the service are created, the app is available for anyone to see:
kubectl apply -f <username_home_directory>/container-service-getting-started-wt/Lab 2/healthcheck.yml
Now that all the deployment work is done, check how everything turned out. You might notice that because more instances are running, things might run a bit slower.

Open a browser and check the app. To form the URL, combine the IP with the NodePort that was specified in the configuration script. To get the public IP address for the worker node, run this command:
ibmcloud cs workers <cluster-name>
In a browser, you’ll see a success message. If you do not see this message, don’t worry. This app is designed to go up and down.

For the first 10 – 15 seconds, a 200 message is returned, so you know that the app is running successfully. After those 15 seconds, a timeout message is displayed as is designed in the app.

Launch your Kubernetes dashboard from the Clusters page in the IBM Cloud Kubernetes Service GUI.
In the Workloads tab, you can see the resources that you created. From this tab, you can continually refresh and see that the health check is working.

In the Pods section, you can see how many times the pods are restarted when the containers in them are re-created.

You might happen to catch errors in the dashboard, indicating that the health check caught a problem. Wait a few minutes and refresh again. You see the number of restart changes for each pod.

``
