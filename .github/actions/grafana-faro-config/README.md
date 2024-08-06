# grafana-faro-config
The provided GitHub Actions workflow, named "grafana faro config," is designed to replace environment variables in a configuration file and copy a configuration file from a source path to a target path. This workflow is commonly used for tasks such as configuring application settings based on environment-specific values.


# Workflow Steps
1. **Replace Grafana ENV**:\
This step uses the sed command to replace environment variable placeholders in a configuration file. It replaces placeholders for the Grafana API key and domain with the actual values provided as inputs.
- The environment variable placeholders are identified by keys:\
 `REACT_APP_GRAFANA_API_KEY` and `REACT_APP_GRAFANA_DOMAIN`.
- The input values provided for the Grafana API key and domain are used to replace these placeholders.
- The sed command updates the configuration file in place, and a backup is created with the .bak extension.
2. **Copy Config File**:\
This step copies a configuration file from the source path to the target path. It uses the cp command for this purpose.


## Inputs
| Name | Description | Default | Required |
|------|-------------|---------|:--------:|
|grafana-api-key|The Grafana API key to be used for configuration||`true`
|grafana-domain|The domain for Grafana||`true`
|source-config-path|The source path of the configuration file||`true`
|target-config-path| The target path for the configuration file. If not provided|`public/local/config.js`|`true`


## Usage
```yaml
steps:
  name: Config grafana faro
  uses: nxz-group/github-shared-workflows/.github/actions/grafana-faro-config@main
  with:
    grafana-api-key: 'grafana-key'
    grafana-domain: 'domain'
    source-config-file: 'configs/config.dev.js'
    target-config-file: 'public/local/config.js'
```

## Example Usage
1. **Create a Workflow File**:\
In your repository, create a directory named `.github/workflows` if it doesn't already exist. Inside this directory, create a YAML workflow file, for example, `grafana-faro-config.yaml`.

2. **Define the Workflow**:\
In the workflow file, define the workflow structure. Here's an example of what the content might look like:
```yaml
name: Grafana Faro Config

on:
  push:

jobs:
  configure-grafana:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout Repository
      uses: actions/checkout@v3

    - name: Configure Grafana
      uses: nxz-group/github-shared-workflows/.github/actions/grafana-faro-config@main
      with:
        grafana-api-key: ${{ secrets.GRAFANA_API_KEY }}
        grafana-domain: ${{ vars.GRAFANA_DOMAIN }}
        source-config-path: 'config/config-template.js'
        target-config-path: 'public/local/config.js'
```

3. **Create secrets and variable**:\
Ensure that the repository where you're using this workflow has the GRAFANA_API_KEY secret set with your Grafana API key. and GRAFANA_DOMAIN variable in your repository.

4. **Commit the Workflow**:\
Commit the workflow file `grafana-faro-config.yaml` to your repository and push the changes.