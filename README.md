# Plugin translation workflows

## Transifex

### Push

This workflow will push the up-to-date locales sources to Transifex.

You can use this workflow using the following Github Actions configuration.

```yaml
name: "Update locales sources"

on:
  push:
    branches:
      - "main"

jobs:
  push-sources-on-transifex:
    name: "Push locales sources"
    uses: "glpi-project/plugin-translation-workflows/.github/workflows/transifex-push-sources.yml@v1"
    secrets:
      # The Transifex API token used to push sources.
      transifex-token: "${{ secrets.TRANSIFEX_TOKEN }}"
```


### Push / pull

This workflow will fetch the latest translations from Transifex and propose a pull request with the incoming changes.

You can use this workflow using the following Github Actions configuration.

```yaml
name: "Synchronize locales"

on:
  schedule:
    - cron: "0 0 * * *"
  workflow_dispatch:

jobs:
  sync-with-transifex:
    name: "Sync with transifex"
    uses: "glpi-project/plugin-translation-workflows/.github/workflows/transifex-sync.yml@v1"
    secrets:
      GLPI_PROJECT_BOT_PRIVATE_KEY: "${{ secrets.GLPI_PROJECT_BOT_PRIVATE_KEY }}"
      # The Transifex API token used to push sources and pull translations.
      transifex-token: "${{ secrets.TRANSIFEX_TOKEN }}"
```

A token other than the workflow `GITHUB_TOKEN` is required, otherwise the pull request would not trigger other workflows.
The workflow accepts, in this order of precedence:

1. `bot-token` secret: a token (PAT or GitHub App token) with the `Contents` and `Pull requests` read/write permissions, used as is.
2. `bot-client-id` input and `bot-private-key` secret. Used by a GitHub App with the `contents: write` and `pull-requests: write` permissions.  
    They default to the `GLPI_PROJECT_BOT_CLIENT_ID` variable and the `GLPI_PROJECT_BOT_PRIVATE_KEY` secret, which must be passed by name.

`github-token` is a deprecated alias of `bot-token`, used when `bot-token` is not set.
