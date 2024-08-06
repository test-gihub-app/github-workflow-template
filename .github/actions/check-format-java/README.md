# check-format-java
The provided GitHub Actions workflow is named "Check Format Java." This workflow is designed to check the code formatting of Java source files in a Java project. It uses Maven and the googlejavaformat-action to format Java files and display the differences.

Here's an explanation of the key components of this workflow:



## Workflow Steps:
1. **Checkout**:\
This step checks out the source code repository using the `actions/checkout@v3` action.
2. **Setup Maven Action**:\
This step sets up the Java and Maven environment using the `s4u/setup-maven-action@main` action. It installs the specified Java and Maven versions.
3. **Java Format**:\
 - This step uses the `axel-op/googlejavaformat-action@v3` to format Java source files. It uses the google-java-format tool to format the code.
 - The args input specifies the arguments for the google-java-format tool, such as --replace to replace the original files with formatted ones.
 - The files input specifies the files to format (Java files by default), and skip-commit is set to true to skip committing the changes.
4. **Print Diffs**:\
This step installs diff-so-fancy globally and then runs git diff to display the differences in a user-friendly format. It also checks the shortstat and exits with a code indicating whether there are differences.

## Inputs
| Name | Description | Default | Required |
|------|-------------|---------|:--------:|
|java-version|The version of Java to use for the action||`true`
|maven-version|The version of Maven to use for the action||`true`
|java-format-files|The files to be checked and formatted|`src/main/**/*.java`|`optional`

## Usage
```yaml
step:
- name: Check Java Code Format
  id: check-format
  uses: nxz-group/github-shared-workflows/.github/actions/check-java-format@main
  with:
    java-version: '11'
    maven-version: '3.8.1'
    java-format-files: 'src/main/**/*.java'
```

## Example Usage
1. **Create a Workflow File**:\
In your repository, create a directory named `.github/workflows` if it doesn't already exist. Inside this directory, create a YAML workflow file, for example, `check-java-format.yaml`.

2. **Define the Workflow**:\
In the workflow file, define the workflow structure. Here's an example of what the content might look like:
```yaml
name: Check Java Code Format

on:
  push:

jobs:
  check-java-format:
    runs-on: ubuntu-latest
    steps:
    - name: Check Java Code Format
      id: check-format
      uses: nxz-group/github-shared-workflows/.github/actions/check-java-format@main
      with:
        java-version: '11'
        maven-version: '3.8.1'
```
3. **Commit the Workflow**:\
Commit the workflow file `check-java-format.yaml` to your repository and push the changes.