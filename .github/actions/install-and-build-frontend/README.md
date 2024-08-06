# install-and-build-frontend
The provided GitHub Actions workflow is named "Install and Build Frontend." This workflow is designed to install Node.js, install frontend project dependencies, and build the frontend application. It also allows you to upload the build artifacts as GitHub artifacts for future reference. Here's an explanation of the key components of this workflow:


## Workflow Steps
1. **Install Node**:\
This step uses the `actions/setup-node@v3` action to install the specified version of Node.js. The Node.js version is determined by the node-version input.

2. **Install Dependencies**:\
Depending on the specified package manager (Yarn or NPM), this step installs the frontend project dependencies using the selected package manager. If neither Yarn nor NPM is selected, an error message is displayed.

3. **Build Frontend**:\
This step runs the build process for the frontend application. It's determined by the selected package manager (Yarn or NPM). If neither Yarn nor NPM is selected, an error message is displayed.

4. **Upload Artifact**:\
If the upload-artifact input is set to `true` this step uses the `actions/upload-artifact@v4` action to upload the build artifacts. The uploaded artifacts will be retained for the specified number of days (default is 15 days).

## Inputs
| Name | Description | Default | Required |
|------|-------------|---------|:--------:|
|node-version|The version of Node.js to use for building the frontend||`true`
|package-manager|The package manager to use (Yarn or NPM)|`npm`|`true`
|upload-artifact|Enable or disable uploading the build artifact as a GitHub artifact|`false`|`true`
|artifact-retention-day| The number of days to retain the uploaded artifact|`15`|`optional`
|frontend-build-path|The folder where the build artifacts will be stored|`build`|`optional`

## Usage
```yaml
steps:
  name: Build frontend
  uses: nxz-group/github-shared-workflows/.github/actions/install-and-build-frontend@main
  with:
    node-version: '18'
    package-manager: 'npm'
    upload-artifact: true
    artifact-retention-day: 15
    frontend-build-path: 'build'
```

## Example Usage
1. **Create a Workflow File**:\
In your repository, create a directory named `.github/workflows` if it doesn't already exist. Inside this directory, create a YAML workflow file, for example, `install-and-build-frontend.yaml`.

2. **Define the Workflow**:\
In the workflow file, define the workflow structure. Here's an example of what the content might look like:
```yaml
name: Build and Deploy Frontend

on:
  push:

jobs:
  build-frontend:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Repository
      uses: actions/checkout@v3

    - name: Run Install and Build Frontend Workflow
      id: build-frontend
      uses: nxz-group/github-shared-workflows/.github/actions/install-and-build-frontend@main
      with:
        node-version: '18'
        package-manager: 'npm'
        upload-artifact: true
        artifact-retention-day: 15
        frontend-build-path: 'build'
```
3. **Commit the Workflow**:\
Commit the workflow file `install-and-build-frontend.yaml` to your repository and push the changes.