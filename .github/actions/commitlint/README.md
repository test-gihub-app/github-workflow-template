# commitlint
The provided GitHub Actions workflow is named "Commitlint." This workflow is designed to validate the format of the last commit message in a Git repository using Commitlint. Commitlint is a tool for checking if your commit messages meet the conventional commit format.


## Workflow Steps:
1. **Checkout**:\
This step checks out the source code repository using the actions/checkout@v3 action. It fetches the entire commit history by setting fetch-depth to 0, ensuring access to the last commit.

2. **Validate Commit Message with Commitlint**:\
This step uses the npx commitlint command to validate the format of the last commit message. The validation is done by comparing the commit message of the last commit (HEAD) to the commit message of the previous commit (HEAD~1) to check if they meet the conventional commit format.
- if: ${{ inputs.enable-commitlint == 'true' }}: This conditional statement checks if the enable-commitlint input is set to 'true' before running the validation. You can enable or disable Commitlint by changing the value of this input.

## Inputs
| Name | Description | Default | Required |
|------|-------------|---------|:--------:|
|enable-commitlint|This input controls whether Commitlint should be enabled or disable. When set to 'true,' the commit message of the last commit will be validated|`true`|`optional`

## Usage
```yaml
steps:
  - name: Validate Commit Message
    uses: nxz-group/github-shared-workflows/.github/actions/commitlint@main
    with:
      enable-commitlint: true
```

## Example Usage
1. **Create a Workflow File**:\
In your repository, create a directory named `.github/workflows` if it doesn't already exist. Inside this directory, create a YAML workflow file, for example, `commitlint.yml`.

2. **Define the Workflow**:\
In the workflow file, define the workflow structure. Here's an example of what the content might look like:
```yaml
name: Commitlint Check

on:
  push:
    branches:
      - main  # Adjust this to your repository's main branch name

jobs:
  commitlint-check:
    runs-on: ubuntu-latest
    steps:
    - name: Validate Commit Message
      uses: nxz-group/github-shared-workflows/.github/actions/commitlint@main
      with:
        enable-commitlint: true
```
3. **Commit the Workflow**:\
Commit the workflow file `commitlint.yml` to your repository and push the changes.