# GCP Cloud Run Gateway
## 1.- Create Gateway SA
```
gcloud iam service-accounts create <gateway-SA-name> \
  --display-name="API Gateway Service Account"
```

## 2.- Deploy the Gateway with Identity
```
gcloud run deploy <gateway-name> \
  --source . \
  --region us-central1 \
  --allow-unauthenticated \
  --service-account <gateway-SA-name>@<project-id>.iam.gserviceaccount.com
```

## 3.- Allow Gateway to invoke backend
```
gcloud run services add-iam-policy-binding <deployment-name> \
  --region us-central1 \
  --member="serviceAccount:<gateway-SA-name>@<project-id>.iam.gserviceaccount.com" \
  --role="roles/run.invoker"
```
