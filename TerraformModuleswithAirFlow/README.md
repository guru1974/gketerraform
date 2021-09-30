# Deploying Airflow on Google Kubernetes Engine

Install Apache Airflow framework (Celery Executer, Cloud Sql database, Redis) to GKE using Helm and Terraform. For details and examples  visit [my blog](https://act-labs.github.io/posts/airflow-gke/ "Installing Airflow on GKE")

## Installation

1. Create cloud infrastructure: Cloud Sql instance, GKE cluster, SSD disk:
```sh
terraform init
terraform apply
```
2. Create service accounts and permissions, enable APIs and configure Workload Identity:
```sh
cd k8s
terraform init
terraform apply
cd ..
```
3. Create Docker image and publish it to Google Container Registry. Docker image is created in two steps. First, we build image with libraries and necessary utilities. Secondly we build image containing which adds sample DAGs:
```sh
# add libraries and third party packages
cd src
docker build -t airflow-python .
# add dags
cd ..
docker build -t airflow-gke:latest .
# push to Google Container repository
docker tag airflow-gke gcr.io/google_project_id/airflow-gke:latest
docker push gcr.io/google_project_id/airflow-gke
```
4. Finally, deploy your application using Helm:
```sh
helm upgrade --install airflow . --set projectId=google_project_id
```
