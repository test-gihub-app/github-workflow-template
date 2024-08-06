# update-image-tag
This GitHub Action, named update-image-tag, is designed to automate the process of updating an image tag in an ArgoCD application configuration. It does so by creating a branch, making the necessary updates, and initiating a pull request to merge the changes.

## Workflow Steps

1. Checkout ArgoCD Repository:
- This step checks out the ArgoCD repository specified in the argocd-repo input using the provided GitHub token.

2. Create Branch for Pull Request:
- A new branch is created with the name pr/{image-tag}. This branch is pushed to the repository to prepare for a pull request.

3. Update Image Tag:
- The specified key in the configuration file is updated with the new image-tag value. Additionally, the "app.kubernetes.io/version" label is updated in the configuration file.

4. Commit and Push Changes:
- The changes made in the previous step are committed and pushed to the repository with a commit message indicating the update.

5. Create Pull Request:
- A pull request is created from the newly created branch to the main branch of the repository. This step uses the gh command-line tool, and the GitHub token is used for authentication.

6. Merge Pull Request (Conditional):
- If the specified environment is not 'prd', this step automatically merges the pull request and deletes the branch. This is a conditional step to control the merge behavior based on the environment.

## inputs
| Name | Description | Default | Required |
|------|-------------|---------|:--------:|
|argocd-repo|The name of the ArgoCD repository where the application configuration is stored||`true`
|image-tag|The new image tag that you want to update in the ArgoCD configuration.||`true`
|argocd-configs-path|The path to the ArgoCD application configuration file where the image tag should be updated.||`true`
|environment|The target environment for the update.||`true`
|key-edit|The key or path in the configuration file that needs to be updated|`.image.tag`|`optional`
|github-token|The GitHub token to perform Git operations and create a pull request.||`true`

## Usage
```
steps:
  name: Build frontend
  uses: nxz-group/github-shared-workflows/.github/actions/update-image-tag@main
  with:
    argocd-repo: 'argocd-nonprd'
    image-tag: 'dev-sha-1-1'
    argocd-configs-path:'configs/oceanxz/exchange-rate-binance'
    environtment: 'dev'
    key-edit: '.image.tag'
    github-token: 'token'
```

## Example Usage
1. **Create a Workflow File**:\
In your repository, create a directory named `.github/workflows` if it doesn't already exist. Inside this directory, create a YAML workflow file, for example, `update-image-tag.yaml`

2. **Define the Workflow**:\
In the workflow file, define the workflow structure. Here's an example of what the content might look like:
```yaml
name: Update Image Tag

on:
  push:

jobs:
  update-image-tag:
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

    - name: Update Image Tag
      id: update-tag
      uses: nxz-group/github-shared-workflows/.github/actions/update-image-tag@main
      with:
        argocd-repo: 'argocd-nonprd'
        image-tag: 'dev-sha-1-1'
        argocd-configs-path: 'configs/oceanxz/exchange-rate-binance'
        environtment: 'dev'
        key-edit: '.image.tag'
        github-token: ${{ steps.generate-token.outputs.token }}
```
3. **Configure GitHub Secrets**:\
For this workflow to work, you'll need to configure the `APP_ID` and `GITHUB_APP_TOKEN` secret in your repository settings.The should contain your GitHub App token with the necessary permissions to update in another repository.
4. **Commit the Workflow**:\
Commit the workflow file update-image-tag.yaml to your repository and push the changes.