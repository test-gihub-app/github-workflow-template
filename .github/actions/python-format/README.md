## Usage
```yaml
steps:
  - name: Pylint
    uses: nxz-group/github-shared-workflows/.github/actions/python-format@main
    with:
      python-directory: 'src'
      requirements: 'requirements.txt'
```