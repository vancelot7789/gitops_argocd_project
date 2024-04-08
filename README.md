# GitOps ArgoCD Project

This repository contains GitOps code responsible for continuous integration via Jenkins and continuous delivery using ArgoCD on a Kubernetes cluster. The application source code used in this project can be found at [https://github.com/vancelot7789/PokemonAuthApi](https://github.com/vancelot7789/PokemonAuthApi).

<br>
<br>

![CICD drawio](https://github.com/vancelot7789/gitops_argocd_project/assets/31930515/306f5dda-8329-4ccd-bdc5-d9127460d9cd)

## Tools

![Jenkins](https://img.shields.io/badge/jenkins-%232C5263.svg?style=for-the-badge&logo=jenkins&logoColor=white)
![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Argo CD](https://img.shields.io/badge/ArgoCD-%23FF9900.svg?style=for-the-badge&logo=argo&logoColor=white&color=orange)

## Getting Started

To get started, you should have Jenkins, ArgoCD, and Kubernetes installed on your local machine, VM, or cloud server.

In the `Jenkinsfile`, replace `credentialsId: 'github'` and `REGISTRY_CRED = "dockerhub"` with your own GitHub and Docker Hub credentials. Additionally, you should upload the image to your Docker Hub account with the corresponding name and tag. Also, update the image in `app-deployment.yaml` to your own image.

## Results

### Jenkins

![Jenkins Screenshot](https://github.com/vancelot7789/gitops_argocd_project/assets/31930515/dd4bea5e-74e1-44cb-a53c-e0ac45997980)

<br>

### ArgoCD

![ArgoCD Screenshot](https://github.com/vancelot7789/gitops_argocd_project/assets/31930515/e0f5c77d-a924-48f2-9f9b-3d655b3f76db)
