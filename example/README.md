# Example Explanation
This example shows a simple project with three nodes: `example_app`, `example_lib`, and `example_test`.

- `example_app` depends on `example_lib`
- `example_test` depends on `example_app`

Whenever the contents of `example_app` are changed, `example_test` will show up in `dependency-cascade query`.
If the contents of `example_lib` are changed, both `example_app` and `example_test` will show up in `dependency-cascade query` because `example_test` depends on `example_app`.

This is a simple example, but in the real world, you might have a more complex project with many dependencies and many tests.

# Pipeline Examples

## Github Actions

```yaml
name: Dependency Cascade Example

on:
  pull_request:
    branches: [ main ]

jobs:
  check-dependencies:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Pull dependency-cascade image
        run: docker pull ghcr.io/tubarao312/dependency-cascade:main
        
      - name: Get changed files
        id: changed-files
        run: |
          echo "files=$(git diff --name-only origin/${{ github.base_ref }} ${{ github.sha }} | jq -R -s -c 'split("\n")[:-1]')" >> $GITHUB_OUTPUT

      - name: Prepare dependency graph
        id: prepare
        run: |
          echo "graph=$(docker run --rm -v $(pwd):/workspace -w /workspace ghcr.io/tubarao312/dependency-cascade:main prepare -d .)" >> $GITHUB_OUTPUT

      - name: Query affected nodes 
        run: |
          echo ${{ steps.prepare.outputs.graph }} | docker run --rm -i -v $(pwd):/workspace -w /workspace ghcr.io/tubarao312/dependency-cascade:main query -g - -f ${{ steps.changed-files.outputs.files }}
```

## Gitlab Pipeline
Help wanted!

## Jenkins Pipeline
Help wanted!

## CircleCI
Help wanted!

