# A repo to deploy pmml models

A part of my CRDS book.

## prerequisites

First you will need to build the `ai-serving` docker image and push it to your image repository. You can run the below on cloud shell.

```
git clone https://github.com/autodeployai/ai-serving.git
cd ai-serving/dockerfiles
IMAGE_NAME="ai-serving"
PROJECT="credit-risk-data-science"
gcloud config set project $PROJECT
docker build -t "gcr.io/${PROJECT}/${IMAGE_NAME}:latest" -f Dockerfile ..
docker push "gcr.io/${PROJECT}/${IMAGE_NAME}:latest"
echo "gcr.io/${PROJECT}/${IMAGE_NAME}:latest"
```

Next there are a few permissions to change so cloud run and cloud build play nice. 

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

## Models format

You can put any PMML model in the `models/` directory. Be sure it ends with `.xml` and contains **no spaces or other special characters**. The model will get deployed to `url.com/v1/models/name` where `name` is derived from the filename.

## Security

This is very insecure as it exposes the API to the public internet with no authentication
