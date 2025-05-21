# Introduction

This repository is part of the autocicd project and is intended solely for maintaining infrastructure values.
🧾 What to Include

Store only environment-specific configuration values here, such as:

- Hostnames
- Image tags (for non-production environments)
- URLs for DSO platform services
-  Any other infrastructure-level settings you know belong here

# Usage
## Values Configuration

Edit the values.yaml file to define shared defaults.

For each environment, create a dedicated values-<env>.yaml file. Override only the values that differ from the default. Reference these environment-specific files in your ArgoCD applications using the ref field.

## GitHub Workflow: Updating Image Tags

Use the update-values.yaml GitHub Actions workflow to automate updates to .image.tag fields for specific environments from another repository.

Example Job
```yaml
jobs:
  update-values:
    name: Update environment image tag
    runs-on: ubuntu-latest
    steps:
      - name: Trigger workflow
        env:
          GH_TOKEN: ${{ secrets.HELM_UPDATER }}
        run: |
          BRANCH_NAME="${{ github.ref_name }}"
          LAST_PART="${BRANCH_NAME##*/}"
          echo "Last part: $LAST_PART"
          gh workflow --repo ${{ github.repository_owner }}/<this-repo-name> run auto-update.yaml -f ENV_NAME=$LAST_PART -f IMAGE_TAG=${{ github.sha }}
```

This script extracts the environment name from the last segment of the branch name (split by /). Feel free to modify this logic to match your branch naming conventions.


## Syncing

Each manual commit and auto-update run a sync job.

TODO: add documentation on variables needed

# `.gitlab-ci-dso.yml`

Do not use this file in production.
It is provided only for internal testing and maintenance. Please delete it after cloning.

# `examples/applications`

These are example sources sections for your ArgoCD applications.

They illustrate how to configure multiple sources — the recommended approach.

ArgoCD does not automatically read these files.

You must copy and adapt their contents into your actual ArgoCD application definitions.