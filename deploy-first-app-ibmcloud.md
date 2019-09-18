# Push an image to IBM Cloud Container Registry

```
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
```
