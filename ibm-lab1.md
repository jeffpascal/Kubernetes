# Lab1

## Push an image to IBM Cloud Container Registry
You use the IBM Cloud Container Registry to push or pull an image.

Clone or download the GitHub repository associated with this course.
Change directory to Lab 1:

cd "Lab 1"

Log in to the IBM Cloud CLI. To specify an IBM Cloud region, include the API endpoint.

If you have a federated ID, use ibmcloud login --sso to log in to the IBM Cloud CLI. You know you have a federated ID if the login fails without the --sso and succeeds with the --sso option.

ibmcloud login

Run ibmcloud cr login and log in with your IBM Cloud credentials. This will allow you to push images to the IBM Cloud Container Registry.

Tip: The commands in this lab show the ng region. Replace ng with the region outputted from the ibmcloud cr login command.

Create a namespace in IBM Cloud Container Registry where you can store your images:

ibmcloud cr namespace-add <my_namespace>

Build the example Docker image:

docker build --tag us.icr.io/<my_namespace>/hello-world .

Verify the image is built:

docker images

Push that image to IBM Cloud Container Registry:

docker push us.icr.io/<namespace>/hello-world

Make sure that the cluster you created previously is ready to use:
Run ibmcloud cs clusters and make sure that your cluster is in Normal state.
Run ibmcloud cs workers <yourclustername> and make sure that all workers are in Normal state with a Ready status.
Make a note of the public IP of the worker.
You are now ready to use Kubernetes to deploy the hello-world application.

## Deploy your application
```
Get the command to set the environment variable and download the Kubernetes configuration files:
ibmcloud cs cluster-config <cluster_name_or_ID>

When the download of the configuration files is finished, a command is displayed that you can use to set the path to the local Kubernetes configuration file as an environment variable, for example, for OS X:

export KUBECONFIG=/Users/<user_name>/.bluemix/plugins/container-service/clusters/pr_firm_cluster/kube-config-prod-par02-pr_firm_cluster.yml

Copy and paste the command that is displayed in your terminal to set the KUBECONFIG environment variable.

Run your image as a deployment:
kubectl run hello-world --image=us.icr.io/<namespace>/hello-world

This action will take a bit of time. To check the status of your deployment, run this command:

kubectl get pods

You should see output like this:

    => kubectl get pods
    NAME                          READY     STATUS              RESTARTS   AGE
    hello-world-562211614-0g2kd   0/1       ContainerCreating   0          1m
When the status reads Running, expose that deployment as a service, which is accessed through the IP of the worker nodes. The example for this lab listens on port 8080. Run this command:
kubectl expose deployment/hello-world --type="NodePort" --port=8080

Find the port that is used on that worker node and examine your new service:
kubectl describe service <name-of-deployment>

Take note of the “NodePort:” line as <nodeport>.

Run the following command and note the public IP as <public-IP>.
ibmcloud cs workers <name-of-cluster>

You can now access your container/service by using curl <public-IP>:<nodeport> (or a web browser). You're done if you see this: “Hello world! Your app is up and running in a cluster!”
```