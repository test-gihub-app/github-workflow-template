# Deploy Frontend to CloudFront with S3

This GitHub Action, named "Deploy Frontend to CloudFront with S3," automates the process of deploying a frontend application to Amazon S3 and invalidating the cache on Amazon CloudFront. It is designed to streamline the deployment of static frontend files to an S3 bucket and ensure that CloudFront reflects the latest changes. Below, you'll find a detailed explanation of the action's inputs, outputs, and the steps it performs.

## Workflow Steps
1. Download Artifact (if enabled):
- This step downloads the specified artifact (frontend files) if download-artifact is set to "true."
2. List View File:
- This step lists the files in the specified frontend build path and captures the file list as an output.
3. Manual Approval (if in "prd" environment):
- If the environtment input is set to "prd," this step uses the nxz-group/manual-approve-action to create a manual approval issue. Approvers from the approvers input are required to approve the deployment. The issue title and body provide details about the deployment, including the list of files.
4. Upload to S3 Bucket:
- This step uses the AWS CLI to synchronize and upload the frontend files to the specified S3 bucket.
5. Invalidate CloudFront Cache:
- This step uses the AWS CLI to create a cache invalidation request for the specified CloudFront distribution. The paths to invalidate are specified in the invalidation-cache-path input.

## Inputs
| Name | Description | Default | Required |
|------|-------------|---------|:--------:|
|s3-bucket-name|The name of the S3 bucket where the frontend files will be uploaded|| `true`
|s3-bucket-region|The AWS region of the S3 bucket|| `true`
|frontend-build-path|The path to the static files produced during the frontend build process||`true`
|cloudfront-id|The ID of the CloudFront distribution||`true`
|invalidation-cache-path|The path to invalidate cache|`/*`|`optional`
|environment|Specifies the environment If set to 'prd,' it requires manual approva If set to any other value, the workflow proceeds automatically||`true`
|approvers|A list of GitHub users in the nxz-group who can approve deployments (applicable for 'prd' environment)||`optional`
|github-app-token|GitHub App token for authorization||`optional`
|download-artifact|A boolean flag to enable or disable the download of artifacts||`true`

## Usage
```yaml
steps:
  name: Build and Push Docker Image
  uses: nxz-group/github-shared-workflows/.github/actions/deploy-frontend-to-cloudfront-with-s3@main
  with:
    s3-bucket-name: 'my-s3-bucket'
    s3-bucket-region: 'us-east-1'
    frontend-build-path: 'build'
    cloudfront-id: 'my-cloudfront-id'
    environtment: 'prd'
    approvers: 'github-user'
    download-artifact: 'true'
    github-app-token: 'token'
```



## Example Usage
1. **Create a Workflow File**:\
In your repository, create a directory named `.github/workflows` if it doesn't already exist. Inside this directory, create a YAML workflow file, for example, `deploy-frontend.yml`.

2. **Define the Workflow**:\
In the workflow file, define the workflow structure. Here's an example of what the content might look like:
```yaml
name: Deploy Frontend

on:
  push:

jobs:
  deploy-frontend:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Repository
      uses: actions/checkout@v2

    - name: Generate token
      id: generate-token
      uses: tibdex/github-app-token@v1
      with:
        app_id: ${{ secrets.APP_ID }}
        private_key: ${{ secrets.APP_PRIVATE_KEY }}

    - name: Deploy Frontend
      id: deploy-frontend
      uses: nxz-group/github-shared-workflows/.github/actions/deploy-frontend-to-cloudfront-with-s3@main
      with:
        s3-bucket-name: 'my-s3-bucket'
        s3-bucket-region: 'us-east-1'
        frontend-build-path: 'build'
        cloudfront-id: 'my-cloudfront-id'
        environtment: 'dev'
        download-artifact: 'true'
        github-app-token: ${{ steps.generate-token.outputs.token }}
```
3. **Configure GitHub Secrets**:\
For this workflow to work, you'll need to configure the `APP_ID` and `GITHUB_APP_TOKEN` secret in your repository settings.The should contain your GitHub App token with the necessary permissions to create issues for manual approvals.
4. **Commit the Workflow**:\
Commit the workflow file `deploy-frontend.yml` to your repository and push the changes.


