# GCP Cloud Run Gateway
## 1.- Create Gateway SA
```
gcloud iam service-accounts create api-gateway-sa \
  --display-name="API Gateway Service Account"
```

## 2.- Deploy the Gateway
```
gcloud run deploy api-gateway \
  --source . \
  --region us-central1 \
  --allow-unauthenticated \
  --service-account api-gateway-sa@PROJECT_ID.iam.gserviceaccount.com
```
