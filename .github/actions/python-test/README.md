## Requirements
```shell
  pip install pytest
  pip install coverage
```

## Usage
```yaml
steps:
  - name: Pylint
    uses: nxz-group/github-shared-workflows/.github/actions/python-test@main
    with:
      python-directory: 'tests'
      requirements: 'requirements.txt'
```