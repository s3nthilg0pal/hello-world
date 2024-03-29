---
layout: post
title: Build and Deploy azure static website using Azure pipeline
subtitle: That too using my hosted agent
---

## Context

When I created this website, I was using github actions to deploy the app to the azure static web app.
Nothing wrong in that.
All works good.

But I have a good pc that is sitting idle doing nothing (I bought it to play games).
So I want to utilise to do some work.

I created a devops agent in it a couple of months ago to practice for my AZ-400 exam.

## What I did?

I decided to use my agent to run my pipeline.

Easy, right?

#### Pipeline Yaml

Simple, create a yaml file `azure-pipelines.yml`.

We are going to define our steps and tasks for our deployment.

```
trigger:
- main

pool:
  vmImage: ubuntu-latest

steps:

- task: AzureStaticWebApp@0
  inputs:
    app_location: "./" # App source code path
    api_location: "" # Api source code path 
    output_location: "_site" # Built app content directory
    azure_static_web_apps_api_token: $(deployment-token)
```

`AzureStaticWebApp` lets us to build and deploy the app. So we dont have to do extra steps such as downloading ruby, jekyll and deploying.

All we need to cofigure the app location and output location in out case is `_site` becaue its a jekyll project.

Oh I forgot, we need to get __deployment-token__ from azure.
![Deployment token](/assets/images/deployment-token.png)

_Commit the file to the github repo_

#### Azure devops

After creating pipeline, we need to the intall this app from Github marketplace [Azure pipelines](https://github.com/marketplace/azure-pipelines)

For creating the actual pipeline, you need to have Azure devops organisations and a project. You can configure it when you install the github app.

Add the token the pipeline variable

![Pipeline variable](/assets/images/pipeline-variable.png)

Save and run the pipeline.

If everything is good, you should see the deployment went through sucessfully.