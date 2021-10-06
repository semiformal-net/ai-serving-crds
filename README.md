# A repo to deploy pmml models

A part of my CRDS book.

## prerequisites

```
PROJECT_ID=credit-risk-data-science
PROJECT_NUMBER=$(gcloud projects describe $PROJECT_ID --format='value(projectNumber)')
# open https://console.cloud.google.com/cloud-build/settings and click "enable API"
# grant cloud run admin to cloud build service account
gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member=serviceAccount:$PROJECT_NUMBER@cloudbuild.gserviceaccount.com \
    --role=roles/run.admin
gcloud iam service-accounts add-iam-policy-binding \
    $PROJECT_NUMBER-compute@developer.gserviceaccount.com \
    --member=serviceAccount:$PROJECT_NUMBER@cloudbuild.gserviceaccount.com \
    --role=roles/iam.serviceAccountUser    
```

Then try to run 

```
gcloud builds submit
```

## Triggering

You can set cloud run to submit this build each time the repository is changed. Once that is done you can add a new PMML model in `models/`. This repo will then track the versions and deployment of all your models!

## Security

This is very insecure as it exposes the API to the public internet with no authentication
