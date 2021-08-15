# AWS EKS w Kubectl

This repository was created for Careem developers to quickly monitor status of their recent deployment in Kubernetes.

## TL;DR

Assuming you've already installed Docker on your machine, following commands will get you up to speed in no time just follow along:

First, build Docker image locally as (required):

```bash
docker build -t waseematcareem/awscli-kubectl:latest 'https://github.com/WaseemAtCareem/awscli-kubectl.git#master'
```

Second, export AWS EKS config (required):

```bash
docker run --rm -it -v ~/.aws:/root/.aws -v ~/.kube:/root/.kube --entrypoint /bin/bash waseematcareem/awscli-kubectl:latest 

aws eks update-kubeconfig --profile NOW_Accounts --region eu-west-1 --name alfa-staging-mot-eks --kubeconfig ~/.kube/kube-staging-mot
```

where `NOW_Accounts` is your AWS profile name, `alfa-staging-mot-eks` is your EKS cluster name which gets stored as `kube-staging-mot`.
Sounds unfamiliar? You probably need to setup your AWS profile. Jump to section <TBA>

Received token expired error?
```bash
An error occurred (ExpiredTokenException) when calling the DescribeCluster operation: The security token included in the request is expired
```

Re-login to AWS CLI, see "Requirements" section below

## Get pods

Lists pods of a service. Replace `robocall-service` with initials of desired service

```bash
docker run --rm -it -v ~/.aws:/root/.aws -v "$(pwd)":/aws -e KUBECONFIG=~/.kube/kube-staging-mot --entrypoint kubectl waseematcareem/awscli-kubectl:latest get pods -n robocall-service 
```

## Get deployments

```bash
TBA
```

## Get logs

```bash
TBA
```


# So, why this repo?

While bootstrapping recently a new service, me and my colleague had to spend couple of hours to get our service deployed in Kubernetes. Despite phenominal tooling and documentation built by Careem K8s team, I felt me and my colleagues need to perform some mondane setup on our machines which was necessary to understand status of our recent deployment. Every engineer needs to install and configure their machine in a very similar fashion otherwise things won't work. I took small effort to ease this pain and created this repo to get Careem engineers basic but vital info asap!

Hope you find it useful ;)

## What is aws-cli?

> aws-cli is the Amazon web services command line interface.

[Overview of awscli](https://docs.aws.amazon.com/cli/index.html)

## What is Kubectl?

> Kubectl is the Kubernetes command line interface.

[Overview of kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)

## Docker File Brief

Using `amazon/aws-cli:2.2.27` as base Docker image and added **Kubectl** `v1.21.0` (pre-compiled binary) alongside **aws-iam-authenticator** `v1.14.6` to bridge aws authentication token obtained by **saml2aws** (runs locally on host, atm).

## Get this image

You will to build the image yourself.

```bash
docker build -t waseematcareem/awscli-kubectl:latest 'https://github.com/waseematcareem/awscli-kubectl.git#master'
```

# Requirements

You must have valid AWS CLI session running. 

Login to AWS:
```bash
saml2aws login --idp-account=wasim.ahmed --username=wasim.ahmed@careem.com
```

saml2aws is missing? See [Account Access](https://docs.sre.red/service-dev/initial-setup.html#account-access)

## Multi AWS Profiles & SAML

* [saml2aws Advanced Configuration](https://github.com/Versent/saml2aws#advanced-configuration)
* AWS profiles are stored in `~/.aws/config`
* AWS credentials with auth & secret tokens are stored in `~/.aws/credentials` (SECURITY RISK)
* SAML profiles are stored in `~/.saml2aws`

## Snippets

```bash
docker run -ti --rm waseematcareem/awscli-kubectl
```

```bash
docker run -ti -e 'AWS_ACCESS_KEY_ID=********************' -e 'AWS_SECRET_ACCESS_KEY=****************************************' -v '${HOME}/.kube:/root/.kube' --rm waseematcareem/awscli-kubectl kubectl get pods --all-namespaces
```

```bash
docker run -ti -v '${HOME}/.aws:/root/.aws' -v '${HOME}/.kube:/root/.kube' --rm waseematcareem/awscli-kubectl kubectl get pods --all-namespaces
```

# Configuration

## Running commands

To run commands inside this container you can use `docker run`, for example to execute `kubectl --version` you can follow the example below:

```bash
docker run --rm --name kubectl waseematcareem/awscli-kubectl:latest -- kubectl version
```

Consult the [Kubectl Reference Documentation](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands) or the [AWS CLI Reference Documentation](https://docs.aws.amazon.com/cli/index.html) to find the completed list of commands available.

## AWS Credentials

AWS credentials can either be passed by environment variables, or by mounting a volume with aws credentials file under `/root/.aws`.

### Environment variables

```bash
docker run -ti -e 'AWS_ACCESS_KEY_ID=***' -e 'AWS_SECRET_ACCESS_KEY=***' --rm waseematcareem/awscli-kubectl aws s3 ls
```

## AWS directory

```bash
docker run -ti -v '${HOME}/.aws:/root/.aws' --rm waseematcareem/awscli-kubectl aws s3
```

## Kubectl credentials

Kubectl credentials can be passed by mounting a volume with the kubeconfig under `/root/.kube`.

## Kubectl directory

```bash
docker run -ti -v '${HOME}/.kube:/root/.kube' --rm waseematcareem/awscli-kubectl kubectl get pods
```

# Contributing

We'd love for you to contribute to this container. You can request new features by creating an [issue](https://github.com/waseematcareem/awscli-kubectl/issues), or submit a [pull request](https://github.com/waseematcareem/awscli-kubectl/pulls) with your contribution.

# Issues

If you encountered a problem running this container, you can file an [issue](https://github.com/waseematcareem/awscli-kubectl/issues). For us to provide better support, be sure to include the following information in your issue:

- Host OS and version
- Docker version (`docker version`)
- Output of `docker info`
- Version of this container
- The command you used to run the container, and any relevant output you saw (masking any sensitive information)

# Credits

This work is forked from `tppalani/awscli-kubectl` which originally forked from `bearengineer/awscli-kubectl` repository.

# License

Copyright (c) 2021 Careem. All rights reserved.
