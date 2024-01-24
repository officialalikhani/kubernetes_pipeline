# Kubernetes CI/CD Deplyment
This repository contains the source code and configuration files for the Project Kubernetes CI/CD Deplyment. The project is built and deployed using GitLab CI/CD pipeline to a Kubernetes cluster.

Before setting up the CI/CD pipeline, make sure you have the following prerequisites installed:

- GitLab account and repository
- Docker installed
- Docker registry (Nexus Repository,Dockerhub)
- Kubernetes cluster

## GitLab CI/CD pipeline
Create a .gitlab-ci.yml file in the root of your repository with the following content:

```bash
stages:
  - build
  - deploy

variables:
  IMAGE_TAG: "1.0.${CI_PIPELINE_IID}"

build:
  stage: build
  image: docker:stable

  before_script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY

  script:
    - docker build -t $CI_REGISTRY_IMAGE:$IMAGE_TAG .
    - docker push $CI_REGISTRY_IMAGE:$IMAGE_TAG

deploy:
  stage: deploy
  image: lachlanevenson/k8s-kubectl:v1.21.0

  before_script:
    - echo "$KUBE_CONFIG" | base64 -d > kubeconfig.yaml

  script:
    - kubectl --kubeconfig=kubeconfig.yaml apply -f deployment.yaml
```
The pipeline consists of two stages:

- Build: This stage builds the Docker image using the Dockerfile and pushes it to the container registry.

- Deploy: This stage deploys the application to the Kubernetes cluster using the updated deployment.yaml file.

## Here Deployment Sample

deployment.yaml:
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: CountReplica
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: <your-registry>/<your-image>:<tag>
        ports:
        - containerPort: YourPort
```

## Push the changes to your GitLab repository
```bash
git add .
git commit -m "Configure CI/CD pipeline"
git push origin main
```
