# build-push-image-to-ecr

This GitHub Action, named build-push-image, is designed to build a Docker image and push it to Amazon Elastic Container Registry (ECR). It is particularly useful for automating your container image deployment process. Below, you'll find information on how to use this action.

## Workflow Steps

1. Configure AWS Credentials
   - This step sets up AWS credentials using the aws-actions/configure-aws-credentials action. It uses the specified AWS region.
2. Generate Image Name
   - This step constructs the full image name by combining the AWS ECR (Elastic Container Registry) registry URL, repository name, and image tag.
   - The constructed image name is stored as an output.

3. Build Docker Image
   - This step builds the Docker image using the specified Dockerfile path and tags it with the image name generated in the previous step.

4. Push Image to ECR
   - This step pushes the Docker image to the specified ECR repository if the push-image input is set to 'true'.
  It also logs a success message indicating the image push.

## Inputs
| Name | Description | Default | Required |
|------|-------------|---------|:--------:|
|image-tag|The tag to be associated with the Docker image|`-`| `true`
|ecr-repository|The name of the Amazon ECR repository where the image will be pushed|`-`| `true`
|dockerfile-path|The path to the Dockerfile to use for building the image. If not specified|`./Dockerfile`|`optional`
|region|The specific AWS region to use|`me-central-1`|`optional`
|push-image|A boolean value to determine whether to push the image to ECR|`true`|`true`

## Outputs
| Name | Description |
|------|-------------|
|image-name|The full name of the Docker image|

## Usage
```yaml
steps:
  name: Build and Push Docker Image
  uses: nxz-group/github-shared-workflows/.github/actions/build-push-image-to-ecr@main
  with:
    image-tag: 'v0.1.0'
    ecr-repository: 'ecr-nxz'
    dockerfile-path: './Dockerfile'
    region: 'me-central-1'
    push-image: true
```
## Example Usage
1. **Workflow File**:\
In your repository, create a directory named `.github/workflows` if it doesn't already exist. Inside this directory, create a YAML workflow file, for example, `build-and-push-image.yaml`
2. **Define the Workflow**:\
In the workflow file, define the workflow structure. Here's an example of what the content might look like:

```yaml
name: Build and Push Docker Image to ECR

on:
  push:

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Build and Push Docker Image
      id: build-push
      uses: nxz-group/github-shared-workflows/.github/actions/build-push-image-to-ecr@main
      with:
        image-tag: 'v0.1.0'
        ecr-repository: 'ecr-nxz'
        push-image: true
        
    - name: Use Docker Image Name
      run: |
        echo "Docker image name: ${{ steps.build-push.outputs.image-name }}"
```
3. **Commit the Workflow**:\
Commit the workflow file `build-and-push-image.yaml` to your repository and push the changes.