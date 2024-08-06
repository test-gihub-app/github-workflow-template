# Retagging Image

The provided GitHub Actions workflow is named "Retagging an image." This workflow is designed to retag a Docker image from a non-production (Nonprd) ECR registry to a production (Prd) ECR registry. This can be useful for promoting container images from a development or staging environment to a production environment. Let's go through the key components of this workflow


## Workflow Steps
1. **Checkout ArgoCD Nonprd Repository**:\
This step uses the actions/checkout@v3 action to check out the repository where the ArgoCD nonprd app configurations are stored. The repository and access token are specified in the inputs.

2. **Get Image Tag**:\
This step retrieves the current image tag from the ArgoCD configuration in the nonprd environment. The image tag is stored in the image_tag variable for later use.

3. **Pull Image from ECR Nonprd**:\
This step pulls the Docker image from the nonprd ECR registry using the image tag obtained in the previous step. The image's full path is stored in the old_image variable.

4. **Retag Image**:\
This step retags the pulled image with the new tag version specified in the inputs. The new tag version is assigned to the image.

5. **Push Image**:\
Finally, this step pushes the retagged image to the prd ECR registry.


## inputs
| Name | Description | Default | Required |
|------|-------------|---------|:--------:|
|argocd-repository|The repository where ArgoCD nonprd app configurations are stored|`nxz-group/argocd-nonprd-apps-eks`|`true`
|argocd-path|The path within the repository where ArgoCD nonprd app configurations are located|`./argocd-nonprd-apps-eks`|`true`
|stg-argocd-configs-path|The path to the ArgoCD configuration file for the nonprd environment||`true`
|ecr-repository|The name of the ECR repository of the application||`true`
|stg-ecr-registry|The ECR registry for the nonprd environment||`true`
|prd-ecr-registry|The ECR registry for the prd environment||`true`
|tag-version|The new tag version to assign to the image||`true`
|github-token|GitHub token for authorization||`true`
|key-edit|The key within the ArgoCD configuration to edit|`.image.tag`|`optional`

## Usege
```yaml
step:
  name: Retag and Push Image
  uses: nxz-group/github-shared-workflows/.github/actions/retagging-image@main
  id: build-app
  with:
    stg-argocd-configs-path: 'oceanxz/stg/config.yaml'
    ecr-repository: 'my-app'
    stg-ecr-registry: '12345.dkr.ecr.us-east-1.amazonaws.com'
    prd-ecr-registry: '67890.dkr.ecr.us-east-1.amazonaws.com'
    tag-version: 'v1.0.1'
    github-token: 'token'
```

## Example Usege
1. **Create a Workflow File**:\
In your repository, create a directory named `.github/workflows` if it doesn't already exist. Inside this directory, create a YAML workflow file, for example, `retag-image.yaml`

2. **Define the Workflow**:\
In the workflow file, define the workflow structure. Here's an example of what the content might look like:
```yaml
name: Retag and Promote Docker Image

on:
  push:

jobs:
  retag-and-promote:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout Repository
      uses: actions/checkout@v3

    - name: Generate token
      id: generate-token
      uses: tibdex/github-app-token@v1
      with:
        app_id: ${{ secrets.APP_ID }}
        private_key: ${{ secrets.APP_PRIVATE_KEY }}

    - name: Run Retagging Workflow
      id: retag
      uses: nxz-group/github-shared-workflows/.github/actions/retag-image@main
      with:
        argocd-repository: 'nxz-group/argocd-nonprd-apps-eks'
        argocd-path: './argocd-nonprd-apps-eks'
        stg-argocd-configs-path: 'oceanxz/stg/config.yaml'
        ecr-repository: 'my-app'
        stg-ecr-registry: '123456789012.dkr.ecr.us-east-1.amazonaws.com'
        prd-ecr-registry: '987654321098.dkr.ecr.us-east-1.amazonaws.com'
        tag-version: 'v1.0.1'
        github-token: ${{ steps.generate-token.outputs.token }}
```

3. **Configure GitHub Secrets**:\
For this workflow to work, you'll need to configure the `APP_ID` and `GITHUB_APP_TOKEN` secret in your repository settings.The should contain your GitHub App token with the necessary permissions to accessing the repository.
4. **Commit the Workflow**:\
Commit the workflow file `retag-image.yaml` to your repository and push the changes.