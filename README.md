# Infisical Secrets Action

This GitHub Action enables you to import secrets from Infisical—whether hosted in the cloud or self-hosted—directly into your GitHub workflows.

## Configuration

- In order to use this, you will need to configure a [Machine Identity](https://infisical.com/docs/documentation/platform/identities/universal-auth) for your project.
- This action supports two ways to authenticate your workflows with Infisical - universal auth and OIDC.

### Universal Auth

- Configure a machine identity to have an auth method of "Universal Auth".
- Get the machine identity's `client_id` and `client_secret` and store them as Github secrets (recommended) or environment variables.
- Set the `client-id` and `client-secret` input parameters.

### OIDC Auth

- Configure a machine identity to use the "OIDC Auth" method. Set the bound audience, bound subject, and bound claims as needed for your setup.
- Get the machine identity's ID.
- Set `method` to oidc and configure the `identity-id` input parameter. Optionally, customize the JWT's aud field by setting the `oidc-audience` input parameter.
- For debugging OIDC configuration issues, you can use GitHub's [actions-oidc-debugger](https://github.com/github/actions-oidc-debugger) tool. This tool helps you inspect the JWT claims and verify they match your configuration.

## Usage

With this action, you can use your Infisical secrets in two ways: as environment variables or as a file.

### As environment variables

Secrets are injected as environment variables and can be referenced by subsequent workflow steps.

```yaml
- uses: Infisical/secrets-action@v1.0.9
  with:
    client-id: ${{ secrets.MACHINE_IDENTITY_CLIENT_ID }} # Update this to your own Github references
    client-secret: ${{ secrets.MACHINE_IDENTITY_CLIENT_SECRET }} # Update this to your own Github references
    env-slug: "dev"
    project-slug: "example-project-r-i3x"
```

### As a file

Exports secrets to a file in your `GITHUB_WORKSPACE`, useful for applications that read from `.env` files.

```yaml
- uses: Infisical/secrets-action@v1.0.9
  with:
    client-id: ${{ secrets.MACHINE_IDENTITY_CLIENT_ID }} # Update this to your own Github references
    client-secret: ${{ secrets.MACHINE_IDENTITY_CLIENT_SECRET }} # Update this to your own Github references
    env-slug: "dev"
    project-slug: "example-project-r-i3x"
    export-type: "file"
    file-output-path: "/src/.env" # defaults to "/.env"
```

**Note**: Make sure to configure an `actions/checkout` step before using this action in file export mode

```yaml
steps:
  - name: Checkout code
    uses: actions/checkout@v4
```

## Inputs

### `method`

**Optional**. The authentication method to use. Defaults to `universal`

### `client-id`

**Optional**. Machine Identity client ID

### `client-secret`

**Optional**. Machine Identity secret key

### `identity-id`

**Optional**. Machine Identity ID

### `oidc-audience`

**Optional**. Custom aud claim for the signed Github ID token

### `project-slug`

**Required**. Source project slug

### `env-slug`

**Required**. Source environment slug

### `domain`

**Optional**. Infisical URL. Defaults to https://app.infisical.com. If you're using Infisical EU (https://eu.infisical.com) or a self-hosted/dedicated instance, you will need to set the appropriate value for this field.

### `export-type`

**Optional**. If set to `env`, it will set the fetched secrets as environment variables for subsequent steps of a workflow. If set to `file`, it will export the secrets in a .env file in the defined file-output-path. Defaults to `env`

### `file-output-path`

**Optional**. The path to save the file when export-type is set to `file`. Defaults to `/.env`

### `secret-path`

**Optional**. Source secret path. Defaults to `/`

### `include-imports`

**Optional**. If set to `true`, it will include imported secrets. Defaults to `true`

### `recursive`

**Optional**. If set to `true`, it will fetch all secrets from the specified base path and all of its subdirectories. Defaults to `false`
