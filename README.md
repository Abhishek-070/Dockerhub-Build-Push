# Dockerhub-Build-Push

## Introduction

The [GitHub Action](https://help.github.com/cn/actions) is used to build Docker images and upload them to docker hub.

- [docker.io repository](https://hub.docker.com)

## Example of Workflow

Create the following yml file, with any file name, using the configuration reference as follows: `.github/workflows/xxxx.yml` :

```yaml
name: Docker Build And Push To Docker Hub

on:
  push:
    branches:
      - master

jobs:
  build:
    name: Build Spring Boot
    runs-on: ubuntu-latest
    steps:
      - name: Git Checkout Code
        uses: actions/checkout@v1
        id: git_checkout

      - name: Set up JDK 12.0
        uses: actions/setup-java@v1
        with:
          java-version: 12.0

      - name: Build with Gradle
        run: ./gradlew build

      - name: Build Docker Image
        id: buildAndPushImage
        uses: abhishek-070/docker-image-build-push-action@v1.0
        with:
          registry_url: 'docker.io'
          repository_name: 'demo-app'
          user_name: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
          image_version: 'v1.0'
          docker_file: '.'
      - name: Get pre step result output image_pull_url
        run: echo "The time was ${{ steps.buildAndPushImage.outputs.image_pull_url }}"
```

Set `registry_url、repository_name、user_name、password` for uploading image to docker hub. For username and password set github secrets so that credentials are not exposed. You can use  `${{ secrets.DOCKERHUB_USERNAME }} ${{ secrets.DOCKERHUB_TOKEN }}` as github secret keys。 For docker hub password generate access token. Ref. [Settings](https://hub.docker.com/settings/security)

## Description

The following parameters can be found in the [docker hub](https://hub.docker.com) documentation 

| Parameter | Required | Description |
| --- | --- | --- |
| registry_url | Yes | Docker hub registry，eg: `docker.io` |
| repository_name | Yes | Docker hub target repository name |
| user_name | Yes | Docker hub username |
| password | Yes | Docker hub access token |
| image_version | Yes | Docker image tag |
| docker_file | No | Context directory, default is（.）|

## Output parameters
After the script is executed, use following parameter for `docker pull`.

| Parameter | Description | 
| --- | --- | 
| image_pull_url | The pull address returned after the successful upload of the image, eg: Use example: `docker.io/abhishek-070/demo-app:v1.0` command: `docker pull ${{ steps.<steps.id>.outputs.image_pull_url }}` |
