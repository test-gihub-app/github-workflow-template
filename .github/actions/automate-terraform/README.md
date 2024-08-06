# automate-terraform
This GitHub Actions workflow named "Automate run terraform" helps automate the process of running Terraform with various checks, including formatting, validation, and planning. Here's a breakdown of the workflow

## Workflow Steps
1. **Checkout to repository**:\
This step checks out your GitHub repository so that subsequent steps can access its content.

2. **Setup Terraform**: It uses the `hashicorp/setup-terraform` action to set up the Terraform CLI.

3. **Configure AWS Credentials**: This step generates AWS credentials in your GitHub runner environment by creating an AWS credentials file in the home directory.

4. **Authenticate to Git with Bot Token**: It authenticates with Git using the GitHub token and sets up Git configuration for the runner.

5. **Terraform Format Check**:\
This step checks the Terraform code formatting using terraform fmt. It's executed only for `pull requests`, and it uses the `-check` flag to ensure the code is correctly formatted.

6. **Terraform Init**:\
This step initializes the Terraform workspace using `terraform init`.

7. **Terraform Validate**:\
This step validates the Terraform configuration using `terraform validate`. Like the format check, it's executed only for pull requests.

8. **Terraform Plan**:\
It creates a Terraform plan using `terraform plan`. This is also executed only for `pull requests`.

9. *Update Pull Request*:\
Using the `actions/github-script` action, this step posts the results of the format check, init, validate, and plan as comments on the `pull request`. It's executed only for pull requests.

10. *Create Manual Approve*:\
If the workflow is triggered on a push event to the `main` branch, it creates a manual approval request. This step uses the `nxz-group/manual-approve-action` and sets the number of required approvals to 1. A GitHub issue is created for this manual approval.

11. **Terraform Apply**:\
If the workflow is triggered on a push event to the `main` branch and manual approval is granted, it applies the Terraform changes using `terraform apply -auto-approve`.


## Inputs
| Name | Description | Default | Required |
|------|-------------|---------|:--------:|
|aws-profile|The name of your AWS profile for authentication||`true`
|aws-access-key|Your AWS access key ID||`true`
|aws-secret-key|Your AWS secret key||`true`
|github-app-token|GitHub app token to authenticate with GitHub||`true`
|work-directory|The path to the Terraform code||`true`
|approvers|GitHub users or teams that should approve the Terraform apply||`true`

## Usage
```yaml
step:
- name: Run automate terraform
  uses: nxz-group/github-shared-workflows/.github/actions/automate-terraform@main
  timeout-minutes: 90
  with:
    aws-profile: 'terraform-nonprd'
    aws-access-key: '123456'
    aws-secret-key: '123456'
    github-app-token: 'token'
    work-directory: 'ap-southeast-1/dev/ec2'
    approvers: 'user-1,user-2'
```

## Example Usage
1. **Create a Workflow File**:\
In your repository, create a directory named `.github/workflows` if it doesn't already exist. Inside this directory, create a YAML workflow file, for example, `automate-terraform.yaml`.

2. **Configure AWS and GitHub Secrets**:\
In your GitHub repository, go to "Settings" > "Secrets" and create the following secrets:
- **APP_ID**: Your GitHub App ID.
- **APP_PRIVATE_KEY**: The private key of your GitHub App.
- **AWS_ACCESS_KEY_ID**: Your AWS access key ID.
- **AWS_SECRET_ACCESS_KEY**: Your AWS secret access key.
- **AWS_PROFILE**: Your AWS profile name.
- **APPROVER**: GitHub user or team responsible for approving Terraform deployments. Specify the GitHub username or team name.

3. **Define the Workflow**:\
In the workflow file, define the workflow structure. Here's an example of what the content might look like:
```yaml
name: Run terraform

on: 
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  directories:
    name: "Directory-changes"
    runs-on: 
      - ubuntu-lastest
    steps:
      - uses: nxz-group/terraform-monorepo-action@main
        id: directories
        with:
          ignore: |
            **/README.md
      - run: echo ${{ steps.directories.outputs.modules }}
    outputs:
      dirs: ${{ steps.directories.outputs.modules }}   

  terraform:
    name: "Terraform"
    if: ${{ needs.directories.outputs.dirs != '[]' }}
    runs-on: 
      - ubuntu-lastest
    needs: 
      - directories
    continue-on-error: ${{ matrix.experimental }}
    strategy:
      matrix:
        experimental: [true]
        directories: ${{ fromJson(needs.directories.outputs.dirs) }}
    defaults:
      run:
        working-directory: ${{ matrix.directories }}
    steps:
      - name: Generate token
        id: generate-token
        uses: tibdex/github-app-token@v1
        with:
          app_id: ${{ secrets.APP_ID }}
          private_key: ${{ secrets.APP_PRIVATE_KEY }}

      - name: Run automate terraform
        uses: nxz-group/github-shared-workflows/.github/actions/automate-terraform@main
        timeout-minutes: 90
        with:
          aws-profile: ${{ vars.AWS_PROFILE }}
          aws-access-key: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          github-app-token: ${{ steps.generate-token.outputs.token }}
          work-directory: ${{ matrix.directories }}
          approvers: ${{ vars.APPROVER }}
```
4. **Commit and Push**:\
Commit the `automate-terraform.yaml` file to your repository and push the changes to trigger the workflow