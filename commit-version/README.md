# Commit Version Action

A simple GitHub Actions composite that updates a version number in a specified file and commits the change.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `file_path` | Path to the file containing the version | Yes | - |
| `version` | New version to set | Yes | - |
| `version_keys` | Comma-separated list of version keys to update | Yes | - |
| `token` | GitHub token for authentication | No | Auto-detected (`github.token` for same repo) |
| `repository` | Target repository (owner/repo format) | No | Current repository |
| `branch` | Target branch | No | `development` |
| `commit_message` | Commit message (use `{version}`, `{version_keys}`, and `{file_path}` placeholders) | No | `chore: update {version_keys} to {version} in {file_path}` |
| `version_pattern` | Regex pattern to find version (use `{key}` placeholder) | No | `{key}:\s*.*` |
| `version_replacement` | Replacement pattern (use `{key}` and `{version}` placeholders) | No | `{key}: {version}` |

## Outputs

| Output | Description |
|--------|-------------|
| `success` | Whether the version was successfully updated |
| `old_version` | Previous version found in file |
| `new_version` | New version that was set |
| `updated_keys` | List of keys that were updated |

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

### Default usage (yml file, multiple keys)

```yaml
  uses: ./.github/actions/commit-version
  with:
    file_path: helm/values.yaml
    version: "v1.2.3"
    version_keys: "app_image_tag, worker_image_tag"
```

This will update:
```yaml
app_image_tag: latest
worker_image_tag: latest
```
to:
```yaml
app_image_tag: v1.2.3
worker_image_tag: v1.2.3
```

### With custom patterns (json file)

```yaml
  uses: ./.github/actions/commit-version
  with:
    file_path: site/package.json
    version: "1.2.3"
    version_keys: "version"
    version_pattern: '"{key}":\s*"[^"]*"'
    version_replacement: '"{key}": "{version}"'
```

### With custom patterns (env file)

```yaml
  uses: ./.github/actions/commit-version
  with:
    file_path: app/version.txt
    version: "2.0.0"
    version_keys: "VERSION"
    version_pattern: '{key}=.*'
    version_replacement: '{key}={version}'
```

### Committing to an external repository

```yaml
  uses: ./.github/actions/commit-version
  with:
    file_path: values.yaml
    version: "1.5.0"
    version_keys: "app_image_tag"
    token: ${{ secrets.PAT_TOKEN }}
    repository: myorg/helm-charts
    branch: development
```
