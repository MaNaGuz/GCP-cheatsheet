# Deploy React app to GCP Firebase using Github Actions

## 1.- Add the firebase.json file at project root

## 2.- Create Firebase SA for Github
```
gcloud iam service-accounts create <sa-name> \
  --display-name=<display name>
```

## 3.- Create JSON key for Firebase SA
```
gcloud iam service-accounts keys create <key-name>.json \
  --iam-account=<sa-name>@<project-id>.iam.gserviceaccount.com
```

## 4.- Copy as repo secret the json key content
```
cat <key-name>
```

## 5.- Grant Firebase SA roles
```
Firebase Hosting Admin
```

## 6.- Add JSON Key to secrets in repo

## 7.- Create GA workflow file

## 8.- create PR or push
