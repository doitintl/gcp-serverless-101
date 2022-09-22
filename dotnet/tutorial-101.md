# Google Cloud Serverless 101

## Overview

This tutorial is a gentle introduction to Serverless Computing on GCP using .NET/C#, brought to you by DoiT International.

## Setup

1. ### Project

<walkthrough-project-setup></walkthrough-project-setup>

2. ### `gcloud` and APIs

- Every `gcloud` command requires a project ID. Set a default project ID so you do not need to provide it every time.

```sh
gcloud config set project {{project-id}}
```

- Set a default region to deploy resources to.

```sh
gcloud config set functions/region <REGION>
```

```sh
gcloud config set run/region <REGION>
```

```sh
gcloud config set artifacts/location <REGION>
```

- Use the following command to enable the required APIs for this tutorial:

```sh
gcloud services enable \
    cloudfunctions.googleapis.com \
    run.googleapis.com \
    cloudbuild.googleapis.com \
    artifactregistry.googleapis.com
```

3. ### **Let's get started!**

## Create a Cloud Function

1. Change to the directory that contains the C# Cloud Function sample code:

```sh
cd dotnet/gcf-hw-http
```

2. Take a look at the sample code: <walkthrough-editor-open-file filePath="gcf-hw-http/Program.cs">Program.cs
   </walkthrough-editor-open-file>

3. Deploy the function

```sh
gcloud functions deploy csharp-http-function \
--gen2 \
--runtime=dotnet3 \
--source=. \
--entry-point=HelloWorld.Function \
--trigger-http \
--allow-unauthenticated
```

## Test and monitor the Cloud Function

1. Make a request to the function:

```sh
gcloud functions describe csharp-http-function --gen2 --format="value(serviceConfig.uri)" | xargs curl
```

2. Open the [Functions Overview page](https://console.cloud.google.com/functions/list) in the Google Cloud console.

3. Look into the `csharp-http-function` function details.

## Create a Cloud Run Service from source

1. Change to the directory that contains the C# Cloud Run sample code:

```sh
cd ../run-hw-source
```

2. Take a look at the sample code: <walkthrough-editor-open-file filePath="run-hw-source/Program.cs">Program.cs
   </walkthrough-editor-open-file>

3. Deploy the Cloud Run service from source:

```sh
gcloud run deploy csharp-run-source \
--source=. \
--allow-unauthenticated
```

## Test and monitor the Cloud Run Service

1. Make a request to the function:

```sh
gcloud run services describe csharp-run-source --format="value(status.address.url)" | xargs curl
```

2. Open the [Cloud Run Overview page](https://console.cloud.google.com/run) in the Google Cloud console.

3. Look into the `csharp-run-source` Cloud Run service details.

## Create a Cloud Run Service with a Dockerfile and Cloud Build

1. Change to the directory that contains the C# Cloud Run sample code:

```sh
cd ../run-hw-docker
```

2. Take a look at the sample Dockerfile: <walkthrough-editor-open-file filePath="run-hw-docker/Dockerfile">Dockerfile
   </walkthrough-editor-open-file>

3. Create an Artifact Registry repository:

```sh
gcloud artifacts repositories create serverless-tutorial --repository-format=docker
```

4. Build the image using Cloud Build

```sh
gcloud builds submit --tag <REGION>-docker.pkg.dev/{{project-id}}/serverless-tutorial/csharp-run-docker:latest
```

5. Deploy

```sh
gcloud run deploy csharp-run-docker --allow-unauthenticated --image <REGION>-docker.pkg.dev/{{project-id}}/serverless-tutorial/csharp-run-docker:latest
```

## Test and monitor the Cloud Run Service

1. Make a request to the function:

```sh
gcloud run services describe csharp-run-docker --format="value(status.address.url)" | xargs curl
```

2. Open the [Cloud Run Overview page](https://console.cloud.google.com/run) in the Google Cloud console.

3. Look into the `csharp-run-docker` Cloud Run service details.

## Clean up

If you created a project specifically for this tutorial, you can [delete the project](https://console.cloud.google.com/cloud-resource-manager) to avoid incurring charges to your account for the resources used in this tutorial.

Otherwise:

- Delete the Cloud Functions

```sh
gcloud functions delete --gen2 csharp-http-function
```

- Delete the Cloud Run Services

```sh
gcloud run services delete csharp-run-source
gcloud run services delete csharp-run-docker
```

- Delete the Artifact Registry repository

```sh
gcloud artifacts repositories delete serverless-tutorial
```

OR

- Delete the project

## Thank You!

Questions?
