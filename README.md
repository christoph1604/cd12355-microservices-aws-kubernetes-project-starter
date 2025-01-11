# Coworking Space Service Extension
The Coworking Space Service is a set of APIs that enables users to request one-time tokens and administrators to authorize access to a coworking space. This service follows a microservice pattern and the APIs are split into distinct services that can be deployed and managed independently of one another.

## Preliminary remark
This repository was originally created by Udacity.com and provided as starting point for project 03 ("Operationalizing a coworking space microservice") within the Udacity nanodegree "Cloud DevOps Engineer". In the original form, the application was intended for local execution.
Final aim of the project was to fork the given repository, to convert the coding in a Docker-based microservice application and deploy it to a Kubernetes cluster with an automated CI/CD pipeline (based on AWS CodeBuild). 

## Dependencies
### Local Environment
1. Python Environment - run Python 3.6+ applications and install Python dependencies via `pip`
2. Docker CLI - build and run Docker images locally
3. `kubectl` - run commands against a Kubernetes cluster
4. `helm` - apply Helm Charts to a Kubernetes cluster

### Remote Resources
1. AWS CodeBuild - build Docker images remotely
2. AWS ECR - host Docker images
3. Kubernetes Environment with AWS EKS - run applications in k8s
4. AWS CloudWatch - monitor activity and logs in EKS
5. GitHub - pull and clone code

## Folder structure
* /db: Coding for database microservice (mainly DB scripts to fill database)
* /analytics: Coding for application microservice
* /deployment: Kubernetes manifests for deployment of database and application microservice
* buildspec.yaml: Configuration of the CI/CD pipeline (to deploy the applications microservice to the ECR repository)
* setup_instructions.md: Setup instructions
* /screenshots: Screenshots of the deployment (for submission of project)
* README.md: This file

## Setup
Due to line limit in submission requirements, for the exact setup steps see file setup_instructions.md