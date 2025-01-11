# Setup 
Instructions for setup of the 'Coworking' application.

## 1. Create infrastructure
1. Create an EKS cluster (Kubernetes version 1.31)
2. Within the cluster, create a node group (node type t3.small, desired size: 1, maximum size: 2)

## 2. Deploy database microservice
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

## 3. Set up CI/CD pipeline with AWS CodeBuild
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

## 4. Deploy application microservice
1. Maintain variables in GitHub project:
* URI of application image in ECR repo
* Host IP of database microservice

2. Deploy application
```bash
kubectl apply -f deployment/configMap.yml
kubectl apply -f deployment/secret.yaml
kubectl apply -f deployment/coworking.yaml
```
## 5. Test Coworking application
```bash
export COWORKING_URL=<URL of application in EKR cluster>
curl $COWORKING_URL:5153/api/reports/daily_usage
curl $COWORKING_URL:5153/api/reports/user_visits
```