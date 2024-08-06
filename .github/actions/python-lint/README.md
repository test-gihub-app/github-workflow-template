## Usage
```yaml
steps:
  - name: Pylint
    uses: nxz-group/github-shared-workflows/.github/actions/python-lint@main
    with:
      python-directory: 'src'
      requirements: 'requirements.txt'
```