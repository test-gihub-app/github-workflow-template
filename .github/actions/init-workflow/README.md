# init-workflow
The provided GitHub Actions workflow is named "Initial Workflow." This workflow is designed to set environment-specific values, including the environment, runner label, and image tag for your project based on the branch and the event that triggered the workflow.


## Workflow Steps
1. **Checkout Repository**:\
This step checks out the source code repository using the `actions/checkout@v3` action.
2. **Generate Environment**:\
This step sets the environment, runner label, and other variables based on the branch and event that triggered the workflow. It uses conditional statements to determine the environment and runner label based on the branch's name.
3. **Generate Short SHA**:\
This step uses the `benjlevesque/short-sha@v2.2` action to generate a short SHA-1 hash for use in the image tag.
4. **Generate Image Tag**:\
This step generates the image tag for the project based on the environment and other information. The image tag includes the environment, a short SHA, the run number, and the run attempt. The generated image tag is stored in the `image-tag` output.
- In the production environment ("prd"), the image tag includes the branch name and the run attempt.
- In other environments (e.g., "dev," "stg," "main"), the image tag includes the generated tag based on the environment, short SHA, run number, and run attempt.

## Inputs
| Name | Description | Default | Required |
|------|-------------|---------|:--------:|
|runner-nonprd|The label of the runner to be used in non-production||`true`
|runner-prd|The label of the runner to be used in the production environment||`true`

## Outputs
| Name | Description |
|------|-------------|
|environment|The environment of the project, which can be one of "dev," "stg," "main," or "prd," depending on the branch and event
|image-tag|The tag for the image, which is generated based on the environment and other information
|runner|The label of the runner to be used for running the workflow.

## Usage
```yaml
 steps:
  - name: Initial Workflow
    id: init-workflow
    uses: nxz-group/github-shared-workflows/.github/actions/init-workflow@main
    with:
      runner-nonprd: 'noprod'
      runner-prd: 'prd'
```

## Example Usage
1. **Create a Workflow File**:\
In your repository, create a directory named `.github/workflows` if it doesn't already exist. Inside this directory, create a YAML workflow file, for example, `init-workflow.yaml`.

2. **Define the Workflow**:\
In the workflow file, define the workflow structure. Here's an example of what the content might look like:
```yaml
name: Initial Workflow

on:
  push:

jobs:
  initial-setup:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Repository
      uses: actions/checkout@v3

    - name: Initialize Workflows
      id: init-workflow
      uses: nxz-group/github-shared-workflows/.github/actions/initial-workflow@main
      with:
        runner-nonprd: 'label-nonprd'
        runner-prd: 'label-prd'

    - name: Environment Setup
      run: |
        echo "Environment: ${{ steps.init-workflow.outputs.environment }}"
        echo "Image Tag: ${{ steps.init-workflow.outputs.image-tag }}"
        echo "Runner: ${{ steps.init-workflow.outputs.runner }}"
```

3. **Commit the Workflow**:\
Commit the workflow file `init-workflow.yaml` to your repository and push the changes.