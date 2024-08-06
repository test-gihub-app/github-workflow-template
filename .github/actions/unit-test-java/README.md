# unit-test-java
Performing unit tests is a crucial part of software development. This GitHub Action simplifies the process by setting up the necessary Java and Maven environment and running unit tests for Java projects.

## Workflow Steps

1. **Checkout Repository**: This step checks out the repository to access the source code and test configurations.

2. **Setup Java and Maven**: The action sets up the specified Java and Maven versions to create the necessary environment for unit testing.

3. **Run Unit Test**: The unit tests are executed using the `mvn test` command, allowing you to verify the correctness of your Java code.

## Inputs
| Name | Description | Default | Required |
|------|-------------|---------|:--------:|
|`java-version`|The version of Java to use for the unit tests||`true`
|`maven-version`|The version of Maven to use for the unit tests||`true`

## Usage
```yaml
steps:
  - name: Run Unit Test
    uses: nxz-group/github-shared-workflows/.github/actions/unit-test-java@main
    with:
      java-version: '17'
      maven-version: '3.9.4'
```

## Example Usage
1. **Create a Workflow File**:\
In your repository, create a directory named `.github/workflows` if it doesn't already exist. Inside this directory, create a YAML workflow file, for example, `unit-test-java.yaml`.

2. **Define the Workflow**:\
In the workflow file, define the workflow structure. Here's an example of what the content might look like:

```yaml
name: "Unit Test Workflow"

on:
  push:
    branches:
      - main

jobs:
  unit_test:
    name: "Unit Test"
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Repository
      uses: actions/checkout@v3

    - name: Run Unit Test
      uses: nxz-group/github-shared-workflows/.github/actions/unit-test-java@main
      with:
        java-version: '17'
        maven-version: '3.9.4'
```
3. **Commit the Workflow**:\
Commit the workflow file `unit-test-java.yaml` to your repository and push the changes.
