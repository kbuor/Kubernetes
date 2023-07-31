# Prerequisites

> This tutorial leverages GCP to streamline provisioning of the compute infrastructure required to bootstrap a Kubernetes cluster from the ground up. Sign up for $300 in free credits.

## Google Cloud Platform SDK

### Install the Google Cloud SDK

> Follow the Google Cloud SDK  to install and configure the gcloud command line utility.

> Verify the Google Cloud SDK version is 301.0.0 or higher:

```shell
$ ./google-cloud-sdk/install.sh
​
$ gcloud version
Google Cloud SDK 302.0.0
bq 2.0.58
core 2020.07.17
gsutil 4.52
```

### Set a Default Compute Region and Zone
```shell
$ gcloud init
​
Welcome! This command will take you through the configuration of gcloud.
​
Your current configuration has been set to: [default]
​
You can skip diagnostics next time by using the following flag:
  gcloud init --skip-diagnostics
​
Network diagnostic detects and fixes local network connection issues.
Checking network connection...done.
Reachability Check passed.
Network diagnostic passed (1/1 checks passed).
​
You must log in to continue. Would you like to log in (Y/n)?  y
​
​
You are logged in as: [xxxxxx@gmail.com].
​
Pick cloud project to use:
 [1] project_a
 [2] kubernetes-the-hard-way-284615
 [3] project_b
 [4] Create a new project
Please enter numeric choice or text value (must exactly match list
item):  2
​
Your current project has been set to: [kubernetes-the-hard-way-284615].
```

> Then be sure to authorize gcloud to access the Cloud Platform with your Google user credentials:

```shell
gcloud auth login
```

> Next set a default compute region and compute zone that is near your place:

```shell
gcloud config set compute/region asia-southeast1
```

> Set a default compute zone:

```shell
gcloud config set compute/zone asia-southeast1-c
```

> Verification:

```shell
$ gcloud config list
​
[compute]
region = asia-southeast1
zone = asia-southeast1-c
[core]
account = hieutran.kt@gmail.com
disable_usage_reporting = False
project = kubernetes-the-hard-way-284615
​
Your active configuration is: [default]
```

> Use the gcloud compute zones list command to view additional regions and zones.
-------------
# Installing the Client Tools
> In this lab you will install the command line utilities required to complete this tutorial: cfssl, cfssljson, and kubectl.
## Install CFSSL
### MAC
> The cfssl and cfssljson command line utilities will be used to provision a  and generate TLS certificates.
```shell
brew install cfssl
```
### Linux
```shell
go install github.com/cloudflare/cfssl/cmd/cfssl@latest
go install github.com/cloudflare/cfssl/cmd/cfssljson@latest
```
### Verification

Verify cfssl and cfssljson version 1.4.1 or higher is installed:

```shell
cfssl version
```
> output
```shell
Version: dev
Runtime: go1.14
```

```shell
cfssljson --version
```

```shell
Version: dev
Runtime: go1.14
```
## Install kubectl
> The kubectl command line utility is used to interact with the Kubernetes API Server. 

```shell
gcloud components install kubectl
​
​
Your current Cloud SDK version is: 302.0.0
Installing components from version: 302.0.0
​
┌─────────────────────────────────────────────────────────────────────┐
│                 These components will be installed.                 │
├─────────────────────┬────────────────────────┬──────────────────────┤
│         Name        │        Version         │         Size         │
├─────────────────────┼────────────────────────┼──────────────────────┤
│ kubectl             │                1.15.11 │             74.7 MiB │
│ kubectl             │             2020.05.01 │              < 1 MiB │
└─────────────────────┴────────────────────────┴──────────────────────┘
```
