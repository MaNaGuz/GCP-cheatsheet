# GCP Cloud Run Gateway
## 1.- Create Gateway SA
```
gcloud iam service-accounts create api-gateway-sa \
  --display-name="API Gateway Service Account"
```

## 2.- Deploy the Gateway with Identity
```
gcloud run deploy api-gateway \
  --source . \
  --region us-central1 \
  --allow-unauthenticated \
  --service-account api-gateway-sa@PROJECT_ID.iam.gserviceaccount.com
```

## 3.- Allow Gateway to invoke backend
```
gcloud run services add-iam-policy-binding api-backend \
  --region us-central1 \
  --member="serviceAccount:api-gateway-sa@PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/run.invoker"
```
