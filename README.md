# filtered-mirror-test

Testing filtered mirroring to GitHub

## Usage

This action filters a repository using `git-filter-repo` and writes the result to a target directory.

### Example

```yaml
- name: Filter repository
  uses: ./.forgejo/mirror-filtered
  with:
    source: src
    target: filtered-repo
    paths: |
      src/
      docs/
    prune_empty: never
```

### Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `source` | Path where the source repository is checked out | `.` |
| `target` | Target directory to write the filtered repository | (required) |
| `paths` | Paths to include in filtered history (multi-line string, one per line) | `""` |
| `prune_empty` | Whether to prune empty commits | `auto` |