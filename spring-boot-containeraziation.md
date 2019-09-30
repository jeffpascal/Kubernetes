
## how to in- [how to install apt-get](#how-to-install-apt-get)
- [how to in- how to install apt-get](#how-to-in--how-to-install-apt-get)
- [How to make a Spring-boot container](#how-to-make-a-spring-boot-container)
  - [Verify if 32 or 64 bit server](#verify-if-32-or-64-bit-server)
  - [Download gcloud for your os version](#download-gcloud-for-your-os-version)
  - [Extract the linux archive](#extract-the-linux-archive)
  - [Install gcloud](#install-gcloud)
``
Search the appropriate version of apt from here (apt_1.4_amd64.deb for ubuntu xenial)

Download the apt.deb

wget http://security.ubuntu.com/ubuntu/pool/main/a/apt/apt_1.4_amd64.deb

Install the apt.deb package

sudo dpkg -i apt_1.4_amd64.deb

Now we can easily run

sudo apt-get install htop

``

## How to make a Spring-boot container

### Verify if 32 or 64 bit server

```
uname -m
```

### Download gcloud for your os version

```
curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-262.0.0-linux-x86_64.tar.gz
```

### Extract the linux archive

- v is verboxe, -xf is to extract
- z??

```
tar zxvf [ARCHIVE_FILE] google-cloud-sdk
```

### Install gcloud

![Imgur](https://imgur.com/lLd9Skl.png)

bash

```
./google-cloud-sdk/install.sh
```

- Use the *gcloud init* command to perform several common SDK setup tasks. These include authorizing the SDK tools to access Google Cloud Platform using your user account credentials and setting up the default SDK configuration.


- To list accounts whose credentials are stored on the local system:

```
gcloud auth list
```

- To list the properties in your active SDK configuration:

```
gcloud config list
```

- To view information about your Cloud SDK installation and the active SDK configuration:

```
gcloud info
gcloud help
```

- For example, to view the help for gcloud compute instances create:

```
gcloud help compute instances create
```













1. sudo apt-get install maven
2. sudo apt-get install default-jdk
3. install docker https://docs.docker.com/v17.12/install/linux/docker-ce/debian/#install-docker-ce-1
4. sudo apt-get install -y python3 python3-pip
5. install docker-machine: https://docs.docker.com/machine/install-machine/
```
base=https://github.com/docker/machine/releases/download/v0.16.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo mv /tmp/docker-machine /usr/local/bin/docker-machine &&
  chmod +x /usr/local/bin/docker-machin
```
6. create docker-machine https://docs.docker.com/machine/drivers/gce/

- To create a machine instance, specify --driver google, the project ID and the machine name.
```
$ gcloud auth login
$ docker-machine create --driver google --google-project PROJECT_ID vm01
$ docker-machine create --driver google \
  --google-project PROJECT_ID \
  --google-zone us-central1-a \
  --google-machine-type f1-micro \
  vm02
```
7. mvn clean install docker:build -Ddocker.host=$DOCKER_HOST






- Add the signed in user to the Docker group so you can run docker commands without sudo and push the image to the repository as an authenticated user using the Container Registry credential helper.

```
sudo usermod -aG docker $USER
```


- Step 2
    - Restart the SSH session so the group change takes effect and return to the kickstart directory.

- Step 3 
    - Store your GCP project name in an environment variable.

```
export GCP_PROJECT=`gcloud config list core/project --format='value(core.project)'`
```

- 