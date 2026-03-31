# Deploy project using Github Actions
## 1.- Create a Project
## 2.- Link a billing account to the project
## 3.- Enable required APIs
We can enable these APIs using GC Console or gcloud cli
### gcloud cli
```
gcloud services enable \
  run.googleapis.com \
  artifactregistry.googleapis.com \
  cloudbuild.googleapis.com \
  logging.googleapis.com \
  iamcredentials.googleapis.com \
  sts.googleapis.com
```
## 4.- Create an Artifact Registry Docker repo
```
gcloud artifacts repositories create <registry-desired-name> \
  --repository-format=docker \
  --location=us-central1 \
  --description="Docker images for Cloud Run"
```
