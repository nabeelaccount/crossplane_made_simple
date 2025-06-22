# Crossplane

Crossplane utilises existing resources and capabilities available in K8s to provision resources including cloud provider resources.

This is an introduction on the use of crossplane. We will be using AWS as a cloud provider in this repository but there is not restriction in using Azure, GCP as as of writing this, Crossplane supports 3 major cloud platforms.

## Prerequisites 

- a Kubernetes cluster with at least 2 GB of RAM
- permissions to create pods and secrets in the Kubernetes cluster
- Helm version v3.2.0 or later
- an AWS account with permissions to create an S3 storage bucket
- AWS access keys

## Install Crossplane 
Install Crossplane using the Crossplane published Helm chart.

Add Helm chart
```sh
helm repo add crossplane-stable https://charts.crossplane.io/stable
helm repo update
```

Install Helm chart
```sh
helm install crossplane \
--namespace crossplane-system \
--create-namespace crossplane-stable/crossplane
```

Verify installation
```sh
kubectl get pods -n crossplane-system
```

Checking Crossplane API end-points with kubectl api-resources
```sh
kubectl api-resources | grep -i crossplane
```

You can find more detailed information in [Crossplane installation](https://docs.crossplane.io/latest/software/install/)

## Configure AWS

### Create Kubernetes secret for AWS
Create a plain txt file containing your AWS secrets for Kuberentes to authenticate to AWS. Name the file aws-credentials.
```sh
cat aws-credentials.txt

[default]
aws_access_key_id =
aws_secret_access_key =
```
Note: If you already have AWS CLI configured on your machine, you can get the above details from ~/.aws/credentials file.


Now create the kubernetes secret for AWS.
```sh
kubectl create secret generic aws-secret -n crossplane-system --from-file=creds=aws-credentials.txt
```

### Apply the AWS provider
Under the folder provider, you will be able to see the AWS provider. The Crossplane Provider installs the Kubernetes Custom Resource Definitions (CRDs) representing AWS S3 services. These CRDs allow you to create AWS resources directly inside Kubernetes.

Please apply the provider
```sh
kubectl apply -f aws_provider.yaml
```
Verify the provider is installed with
```sh
kubectl get providers
```

Apply the configuration to tell Crossplane the credentials it needs to authenticate with
```sh
kubectl apply -f aws_provider.yaml
```

### Create S3 bucket
Under aws_resources folder we will create an AWS S3 bucket and it's associated crontrol policy, utilising the providers we created previously.
```sh
kubectl apply -f s3bucket.yaml
```

You can verify on:
1. Kubernetes
```sh
kubectl get bucket
```
2. AWS using Console or CLI as shown below
```sh
aws s3 ls
```

For further information please visit Crossplane [AWS Quickstart](https://docs.crossplane.io/latest/getting-started/provider-aws/)
