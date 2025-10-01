# Commit Version Action

A GitHub Actions composite that updates one or more version values in a target file and commits the change.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `file_path` | Path to the file containing the version | Yes | - |
| `versions` | Comma-separated list of versions (positional to `version_keys`) | Yes | - |
| `version_keys` | Comma-separated list of version keys to update (must match `versions` length) | Yes | - |
| `token` | GitHub token for authentication | No | Auto-detected (`github.token` for same repo) |
| `repository` | Target repository (owner/repo format) | No | Current repository |
| `branch` | Target branch | No | `development` |
| `commit_message` | Commit message (placeholders: `{file_path}`, `{version_keys}`, `{versions}`, `{version_pairs}`) | No | `chore: update {version_pairs} in {file_path}` |
| `version_pattern` | Regex pattern to find version (use `{key}` placeholder) | No | `{key}:\s*.*` |
| `version_replacement` | Replacement pattern (use `{key}` and `{version}` placeholders) | No | `{key}: {version}` |

## Outputs

| Output | Description |
|--------|-------------|
| `success` | Whether the version was successfully updated |
| `old_versions` | Comma-separated list of previous versions (aligned to `version_keys`) |
| `new_versions` | Comma-separated list of new versions (aligned to `version_keys`) |
| `updated_keys` | List of keys that were updated |
| `version_pairs` | Comma-separated `key=version` pairs after update |

## Authentication

- **Same repository**: Automatically uses `${{ github.token }}` (no token input required)
- **External repository**: Requires explicit `token` input (Personal Access Token)

### Same Repository
```yaml
permissions:
  contents: write
```

### External Repositories

1. Create a Personal Access Token with `repo` scope
2. Add it as a repository secret (e.g., `PAT_TOKEN`)
3. Pass it explicitly in the `token` input

## Examples

### Multiple keys with distinct versions (YAML file)

```yaml
  uses: ./.github/actions/commit-version
  with:
    file_path: path/values.yaml
    version_keys: "app_image_tag, worker_image_tag"
    versions: "app-v1.2.3, worker-v1.2.3"
    commit_message: "chore: update {version_pairs} in {file_path}"
```

This will update:
```yaml
app_image_tag: old-app-version
worker_image_tag: old-worker-version
```
to:
```yaml
app_image_tag: app-v1.2.3
worker_image_tag: worker-v1.2.3
```

### Json file pattern

```yaml
  uses: ./.github/actions/commit-version
  with:
    file_path: path/package.json
    version_keys: "version"
    versions: "1.2.3"
    version_pattern: '"{key}":\s*"[^"]*"'
    version_replacement: '"{key}": "{version}"'
```

### Env file pattern

```yaml
  uses: ./.github/actions/commit-version
  with:
    file_path: path/version.txt
    version_keys: "VERSION"
    versions: "2.0.0"
    version_pattern: '{key}=.*'
    version_replacement: '{key}={version}'
```

### Committing to an external repository

```yaml
  uses: ./.github/actions/commit-version
  with:
    file_path: path/values.yaml
    version_keys: "app_image_tag"
    versions: "1.5.0"
    token: ${{ secrets.PAT_TOKEN }}
    repository: myorg/helm-charts
    branch: development
```
