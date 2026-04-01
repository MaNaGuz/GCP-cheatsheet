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

## 5.- Setup some ENV variables
```
export PROJECT_ID="<your-project-id>"
export PROJECT_NUMBER="$(gcloud projects describe "$PROJECT_ID" --format='value(projectNumber)')"
export REGION="us-central1"
export REPO="stock-tracker-python-react"
export GITHUB_OWNER="<Gihub Owner>"
export GITHUB_REPO="<Github repo name>"
export POOL_ID="github"
export PROVIDER_ID="github-provider"
export SA_NAME="github-cloudrun-deployer"
export SA_EMAIL="${SA_NAME}@${PROJECT_ID}.iam.gserviceaccount.com"
```

## 6.- Create deployer SA and grant roles
```
gcloud iam service-accounts create "$SA_NAME" \
  --project="$PROJECT_ID" \
  --display-name="GitHub Cloud Run Deployer"

gcloud projects add-iam-policy-binding "$PROJECT_ID" \
  --member="serviceAccount:${SA_EMAIL}" \
  --role="roles/run.admin"

gcloud projects add-iam-policy-binding "$PROJECT_ID" \
  --member="serviceAccount:${SA_EMAIL}" \
  --role="roles/iam.serviceAccountUser"

gcloud projects add-iam-policy-binding "$PROJECT_ID" \
  --member="serviceAccount:${SA_EMAIL}" \
  --role="roles/artifactregistry.writer"

gcloud projects add-iam-policy-binding "$PROJECT_ID" \
  --member="serviceAccount:${SA_EMAIL}" \
  --role="roles/cloudbuild.builds.editor"
```

## 7.- Create Workload Identity Pool
```
gcloud iam workload-identity-pools create "$POOL_ID" \
  --project="$PROJECT_ID" \
  --location="global" \
  --display-name="GitHub Actions Pool"
```

## 8.- Create Github Provider
```
gcloud iam workload-identity-pools providers create-oidc "github-provider" \
  --project="$PROJECT_ID" \
  --location="global" \
  --workload-identity-pool="github" \
  --display-name="GitHub Provider" \
  --issuer-uri="https://token.actions.githubusercontent.com" \
  --attribute-mapping="google.subject=assertion.sub,attribute.actor=\
    assertion.actor,attribute.repository=assertion.repository,attribute.repository_owner=\
    assertion.repository_owner" \
  --attribute-condition="assertion.repository_owner=='MaNaGuz'"
```

## 9.- Let repo impersonate the SA
```
gcloud iam service-accounts add-iam-policy-binding "$SA_EMAIL" \
  --project="$PROJECT_ID" \
  --role="roles/iam.workloadIdentityUser" \
  --member="principalSet://iam.googleapis.com/projects/${PROJECT_NUMBER}/locations/global/workloadIdentityPools/\
    ${POOL_ID}/attribute.repository/${GITHUB_OWNER}/${GITHUB_REPO}"
```

## 10.- Put Secrets in Github repo environment secrets and vars
`WIF_PROVIDER` From IAM -> Workload Identity Federation -> <your-pool-name> -> Providers -> ignore https: and start with projects
`WIF_SERVICE_ACCOUNT` From IAM -> Service Accounts -> <your-sa> -> email
`GCP_PROJECT_ID`
`GCP_REGION`
