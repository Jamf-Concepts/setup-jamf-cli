# setup-jamf-cli

GitHub Action to install [jamf-cli](https://github.com/Jamf-Concepts/jamf-cli) and add it to `PATH`.

Supports Linux (`amd64`, `arm64`) and macOS (universal binary).

## Usage

```yaml
- uses: Jamf-Concepts/setup-jamf-cli@v1
```

### Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `version` | Version to install (`1.3.0`, `v1.3.0`, or `latest`) | `latest` |
| `token` | GitHub token used to resolve the latest version | `${{ github.token }}` |
| `no-color` | Set `NO_COLOR=1` for all subsequent steps (disables ANSI color in output) | `false` |

### Outputs

| Output | Description |
|--------|-------------|
| `version` | The installed version (without `v` prefix) |

## Examples

### Latest version

```yaml
steps:
  - uses: Jamf-Concepts/setup-jamf-cli@v1

  - name: List computers
    env:
      JAMF_URL: ${{ secrets.JAMF_URL }}
      JAMF_CLIENT_ID: ${{ secrets.JAMF_CLIENT_ID }}
      JAMF_CLIENT_SECRET: ${{ secrets.JAMF_CLIENT_SECRET }}
    run: jamf-cli pro computers list --quiet --no-input
```

### Recommended CI pattern

Use `--quiet` to suppress non-error output and `--no-input` to fail fast if a command unexpectedly requires interactive input rather than hanging.

```yaml
steps:
  - uses: Jamf-Concepts/setup-jamf-cli@v1
    with:
      no-color: 'true'

  - name: Apply building
    env:
      JAMF_URL: ${{ secrets.JAMF_URL }}
      JAMF_CLIENT_ID: ${{ secrets.JAMF_CLIENT_ID }}
      JAMF_CLIENT_SECRET: ${{ secrets.JAMF_CLIENT_SECRET }}
    run: |
      jamf-cli pro buildings apply \
        --from-file building.json \
        --yes \
        --quiet \
        --no-input
```

### Pinned version

```yaml
steps:
  - uses: Jamf-Concepts/setup-jamf-cli@v1
    with:
      version: 'v1.3.0'
```

### Capture installed version

```yaml
steps:
  - uses: Jamf-Concepts/setup-jamf-cli@v1
    id: setup

  - run: echo "Installed ${{ steps.setup.outputs.version }}"
```

## Credentials

Use environment variables — not config profiles — in CI:

| Variable | Description |
|----------|-------------|
| `JAMF_URL` | Jamf Pro instance URL |
| `JAMF_CLIENT_ID` | OAuth2 client ID |
| `JAMF_CLIENT_SECRET` | OAuth2 client secret |
| `JAMF_TOKEN` | Pre-existing bearer token (alternative to OAuth2) |
| `JAMFPROTECT_URL` | Jamf Protect tenant URL |
| `JAMFPROTECT_CLIENT_ID` | Jamf Protect OAuth2 client ID |
| `JAMFPROTECT_CLIENT_SECRET` | Jamf Protect OAuth2 client secret |

Store these as [encrypted secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) in your repository or organisation settings.

## Supported platforms

| Runner | Supported |
|--------|-----------|
| `ubuntu-latest` | ✅ |
| `macos-latest` | ✅ |
| `windows-latest` | ❌ |
