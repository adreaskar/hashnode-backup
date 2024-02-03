---
title: "How to build and push a Docker image using GitHub actions 🤖"
seoTitle: "How to build and push a Docker image using GitHub actions"
seoDescription: "In this article we will use an automated pipeline to build Docker images and then push them to Docker Hub remote registry."
datePublished: Thu Sep 08 2022 13:45:25 GMT+0000 (Coordinated Universal Time)
cuid: cl7t3qon407aoecnvd7kn3ch6
slug: how-to-build-and-push-a-docker-image-using-github-actions
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1662712462242/ueXq3309p.png
tags: docker, github, ci-cd, docker-images, github-actions-1

---

In this tutorial, we will use an automated pipeline to build Docker images and then push them to a remote registry. For this example, we will use [Docker Hub](https://hub.docker.com/).

As always, here is the [official documentation](https://docs.github.com/en/actions) link for GitHub actions.

# Prerequisites

Before we start implementing our pipeline, we need to set up our container registry and GitHub repository.

### Docker Hub

Our action will require access to a Docker Hub repository. The preferred method of authentication is the use of an access token. Here's what you need to do:

1. Create an account on Docker Hub
2. Create a repository with the desired name of your image
3. Click on **Account settings** -> **Privacy**, and generate a new access token.

> Note: Make sure to save the token's value because it will not appear again after a page refresh. 

### GitHub

Select a GitHub repository where you want to implement the automated action and follow these steps:

1. Click on **Settings** -> **Secrets** -> **Actions** and select **New repository secret**.
2. Create a secret named **DOCKERHUB_USERNAME** and add your Docker Hub username as a value.
3. Create another one named **DOCKERHUB_TOKEN** and add your Docker Hub access token that you generated earlier as a value.

# Creating our action

Once we have the container registry and GitHub repo set up, we can create the workflow file. To do that:

1. Go to your GitHub repository and click on the tab **Actions**.
2. There you will find some suggested pre-built workflows, and an option to **set up a workflow yourself**. Click on that.

### Writting the file

Each workflow is represented by a `.yml` document. 

Below is our **Docker image build and push** .yml file.

```yml
name: Docker image build and push

on:
  push:
    branches: [ "master" ]
  pull-request:

jobs:

  build-push:

    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        
      - name: Login to DockerHub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
          
      - name: Build and push
        uses: docker/build-push-action@v3
        with:
          context: .
          push: true
          tags: <username>/<repository>:latest
```

Here's what's going on:

1. `name:` The name of your GitHub action.
2. `on:` Controls when your action will run.
2. `push:` and `pull-request:` Triggers the workflow on every push request at the master branch, and every pull-request.
3. `jobs:` A workflow consists of one or many jobs that can run sequentially or in parallel. For this example, we only use one job named **build-push**
4. `runs-on:` The OS you want for the runner environment that executes the workflow. For this example, we will use **Ubuntu ** as it has Docker pre-configured. Other popular options are **windows-latest** or **macos-latest**.
5. `steps:` Each step runs in its own process in the runner environment. Steps consist of:
 - `name: ` Describes each step.
 - `uses:` Selects a pre-built action to run as part of a step in your job.

     For example here we are using the **actions/checkout@v3**, **docker/login-action@v2** and **docker/build-push-action@v3**, as part of our workflow.

      You can also use any custom action from the [GitHub actions marketplace](https://github.com/marketplace?type=actions).
  - `with:` Input parameters for this step's pre-built action.

    For example, in the second step, we pass as parameters a `username:` and `password:`. Notice how the values are drawn from the **repository secrets** that we set up earlier.

    In the third step, we have a `context:` parameter that indicates the **location of our Dockerfile**, a `push:` parameter set to **true** and a `tags:` parameter where we can define what tags we want for our Docker Hub repository.

More on GitHub actions syntax [here](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions).

### Deploying the workflow

That was it! 🎉 

All that's left to do is deploy our action for the first time, and we can do that by **committing** this file to our repository. This will save it under `.github/workflows/docker-image.yml`, and trigger the workflow. 

After the image gets built, it will be uploaded to your Docker Hub repository with the tag `:latest`.

You can find the overview of each workflow under the **Actions** tab on your GitHub repository, as well as a detailed log of each job step.

Till next time! ✌️

