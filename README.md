# Setup Kanuka Action

A GitHub Action to set up [Kanuka](https://github.com/PolarWolf314/kanuka) CLI for encrypted secrets management in your workflows.

## Usage

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Kanuka
        uses: PolarWolf314/kanuka-actions@v1
        with:
          private-key: ${{ secrets.KANUKA_PRIVATE_KEY }}

      - name: Decrypt secrets
        run: cat "$KANUKA_PRIVATE_KEY_PATH" | kanuka secrets decrypt --private-key-stdin

      - name: Use secrets
        run: |
          source .env
          ./deploy.sh
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `private-key` | Private key content for decryption | Yes | - |
| `passphrase` | Passphrase for the private key, if encrypted | No | `''` |
| `version` | Kanuka version to install (e.g., `1.2.0` or `latest`) | No | `latest` |

## Outputs

| Output | Description |
|--------|-------------|
| `private-key-path` | Path to the private key file |

## Environment Variables

After running this action, the following environment variables are available:

| Variable | Description |
|----------|-------------|
| `KANUKA_PRIVATE_KEY_PATH` | Path to the private key file |
| `KANUKA_PASSPHRASE` | Passphrase (only set if provided) |

## Examples

### Basic decryption

```yaml
- uses: PolarWolf314/kanuka-actions@v1
  with:
    private-key: ${{ secrets.KANUKA_PRIVATE_KEY }}

- run: cat "$KANUKA_PRIVATE_KEY_PATH" | kanuka secrets decrypt --private-key-stdin
```

### With passphrase-protected key

```yaml
- uses: PolarWolf314/kanuka-actions@v1
  with:
    private-key: ${{ secrets.KANUKA_PRIVATE_KEY }}
    passphrase: ${{ secrets.KANUKA_PASSPHRASE }}

- run: cat "$KANUKA_PRIVATE_KEY_PATH" | kanuka secrets decrypt --private-key-stdin
```

### Pin to specific version

```yaml
- uses: PolarWolf314/kanuka-actions@v1
  with:
    private-key: ${{ secrets.KANUKA_PRIVATE_KEY }}
    version: '1.2.0'
```

## Setup

### 1. Generate a keypair (if you haven't already)

```bash
kanuka secrets create --email ci@yourcompany.com
```

### 2. Register the CI user

```bash
kanuka secrets register --user ci@yourcompany.com
```

### 3. Add the private key to GitHub Secrets

1. Copy the contents of your private key file
2. Go to your repository → Settings → Secrets and variables → Actions
3. Create a new secret named `KANUKA_PRIVATE_KEY`
4. Paste the private key contents

### 4. Use the action in your workflow

See the usage examples above.

## Security

- Private keys are written to `$RUNNER_TEMP` with `600` permissions
- All secret values are masked in logs using `::add-mask::`
- Keys are automatically cleaned up when the runner terminates

## License

MIT
