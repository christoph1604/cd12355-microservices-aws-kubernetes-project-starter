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
* README.md: This file

## Setup
### 1. Create infrastructure
1. Create an EKS cluster (Kubernetes version 1.31)
2. Within the cluster, create a node group (node type t3.small, desired size: 1, maximum size: 2)

### 2. Deploy database microservice
1. Set up the database microservice using the following commands:

```bash
aws eks --region us-east-1 update-kubeconfig --name <CLUSTERNAME>
kubectl apply -f deployment/pvc.yaml
kubectl apply -f deployment/pv.yaml
kubectl apply -f deployment/postgresql-deployment.yaml
kubectl apply -f deployment/postgresql-service.yaml
```

2. Fill the database: 
```bash
apt update
apt install postgresql postgresql-contrib

export DB_HOST=<HOST NAME>
export DB_PORT=<PORT>
export DB_NAME=<DATABASE NAME>
export DB_USER=<USER NAME>
export DB_PASSWORD=<PASSWORD>

kubectl port-forward service/postgresql-service $DB_PORT:5432 &
PGPASSWORD="$DB_PASSWORD" psql --host $DB_HOST -U $DB_USER -d $DB_NAME -p $DB_PORT < ./db/1_create_tables.sql
PGPASSWORD="$DB_PASSWORD" psql --host $DB_HOST -U $DB_USER -d $DB_NAME -p $DB_PORT < ./db/2_seed_users.sql
PGPASSWORD="$DB_PASSWORD" psql --host $DB_HOST -U $DB_USER -d $DB_NAME -p $DB_PORT < ./db/3_seed_tokens.sql
```

3. Test the database microservice
```bash
PGPASSWORD="$DB_PASSWORD" psql --host $DB_HOST -U $DB_USER -d $DB_NAME -p $DB_PORT
select *from users limit 10;
```

### 3. Set up CI/CD pipeline with AWS CodeBuild
1. Create ECR repo <NS>/<REPO_NAME>
2. Create CodeBuild project <MY_CODEBUILD_PROJECT> 
Configure the project in a way that code build are triggered via a Webhook referencing the GitHub repository. 
Define the following environment variables and fill them corresponding to your implementation:
* AWS_DEFAULT_REGION
* AWS_ACCOUNT_ID
* IMAGE_NS
* IMAGE_REPO_NAME
* DB_USERNAME
* DB_PASSWORD
3. Trigger the CodeBuild pipeline either manually via the AWS Console/CLI or via a commit to the GitHub repository.

### 4. Deploy application microservice
1. Maintain variables in GitHub project:
* URI of application image in ECR repo
* Host IP of database microservice

2. Deploy application
```bash
kubectl apply -f deployment/configMap.yml
kubectl apply -f deployment/secret.yaml
kubectl apply -f deployment/coworking.yaml
```
### 5. Test Coworking application
```bash
export COWORKING_URL=<URL of application in EKR cluster>
curl $COWORKING_URL:5153/api/reports/daily_usage
curl $COWORKING_URL:5153/api/reports/user_visits
```