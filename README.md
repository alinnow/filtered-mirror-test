# filtered-mirror-test

Filter a repository history for mirroring to an untrusted forge.

## Use case

Want to make your projects discoverable on centralised forges, but don't want to support the centralisation? Use this action to mirror only a readme and license file, so that interested users can find your repository on a centralised site, but have to leave it to use your code.

## Usage

This action filters a repository using `git-filter-repo` and writes the result to a target directory.

### Example

```yaml
on:
  push:
    branches:
      - main

jobs:
  mirror:
    runs-on: codeberg-tiny-lazy
    steps:
      - name: Check out the repository
        uses: actions/checkout@v6
        with:
          fetch-depth: 0

      - name: Check out the target repository
        run: |
          git clone github.com/${{ forgejo.repository }} target || {
            git init --initial-branch=${{ forgejo.ref_name }} target &&
            git remote set-url origin https://github.com/${{ forgejo.repository }}.git
          }

      - name: Filter repository
        uses: alinnow/mirror-filtered
        with:
          source: .
          target: target
          paths: |
            README.md
            LICENSE
          prune_empty: never

      - name: Push filtered repository
        run: |
          cd target
          git push --force --mirror https://${{ secrets.OTHER_FORGE_TOKEN }}@github.com/${{ forgejo.repository }}
```

### Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `source` | Path where the source repository is checked out | `.` |
| `target` | Target directory to write the filtered repository | (required) |
| `paths` | Paths to include in filtered history (multi-line string, one per line) | `""` |
| `path_globs` | Path globs to include in filtered history (multi-line string, one per line) | `""` |
| `prune_empty` | Whether to prune empty commits | `auto` |

### How it works

1. The action clones the source repository
2. Applies path filters using `git-filter-repo --path` and `--path-glob`
3. Writes the filtered history to the target directory
4. A subsequent step can push the filtered repository to a remote location
