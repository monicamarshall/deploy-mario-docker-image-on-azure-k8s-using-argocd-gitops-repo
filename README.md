# Docker Super Mario Project

## Overview
The Docker Super Mario Project is a modern adaptation of the classic Infinite Mario game, reimagined using HTML5, JavaScript, Canvas, and Audio elements. This project serves as an exemplary platform for learning and implementing GitOps pipelines, offering a hands-on approach to continuous integration and continuous deployment (CI/CD) practices.

## Features
- **HTML5 Canvas**: Delivers dynamic, scriptable rendering of 2D shapes and bitmap images.
- **JavaScript**: Ensures interactive game mechanics and responsive design.
- **Audio Elements**: Enhances the gaming experience with authentic sound effects and background music.
- **Docker Integration**: Facilitates the deployment of the application in isolated environments, making it easy to share and scale.
- **GitOps Workflow**: Introduces participants to modern DevOps practices, focusing on automation, monitoring, and version control.

## e2e-gitops.yaml supersedes gitops-build-push-supermario-image.yaml

# superseds .github/workflows/gitops-build-push-supermario-image.yaml
#
# Why: The old workflow only builds and pushes a new Docker image tag
# (VERSION) to Docker Hub. It does NOT update the Kubernetes manifest
# (deployment.yaml) with the new tag.
#
# In Argo CD (pull-based GitOps), deployments are driven by Git changes,
# not by images appearing in the registry. If deployment.yaml doesn’t
# change, Argo CD sees no drift and won’t roll out the new image.
#
# The new e2e-gitops.yaml fixes this by:
#   1) bumping version.txt,
#   2) updating deployment.yaml to the new image tag,
#   3) committing those changes to the manifests repo/branch watched by Argo CD.
#
# Result: Argo CD detects the Git change and (with Auto-Sync enabled)
# applies the new image to the cluster.
# GitHub Actions run on repo events (like push to main) as defined in your workflow on: block.
# Argo CD continuously (or via webhook) watches the Git repo/branch+path 
# configured in the Argo CD Application. When a commit changes manifests 
# (like deployment.yaml), Argo CD detects it and:
# If Auto-Sync is ON → it deploys automatically.
# If Auto-Sync is OFF → it shows “OutOfSync” and you click Sync (or run argocd app sync).
# “If we don’t update deployment.yaml with the new image tag (old action) Argo CD 
# keeps the previously committed image/tag. No Git change → no new deploy.
# If the workflow only pushes a new image tag to DockerHub but doesn’t change deployment.yaml, 
# the new image is in Docker Hub but not deployed by Argo CD. 
# That’s why your new e2e-gitops.yaml commits the tag change to Git (sed substitution command).