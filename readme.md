# Kubernetes CI/CD Deplyment
This repository contains the source code and configuration files for the Project Kubernetes CI/CD Deplyment. The project is built and deployed using GitLab CI/CD pipeline to a Kubernetes cluster.
Prerequisites

Before setting up the CI/CD pipeline, make sure you have the following prerequisites installed:

- GitLab account and repository
- Docker installed
- Docker registry
- Kubernetes cluster

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
