# enterprise-repository-inventory
Collects repository statistics for every organization in a GitHub Enterprise using
[gh-repo-stats-plus](https://github.com/mona-actions/gh-repo-stats-plus), via the
`All Enterprise Organization Repository Stats` workflow.

## Authentication

The workflow's `auth` input selects how it authenticates.

### `github-app` (default)

Uses two GitHub Apps owned by the enterprise, following
[Automating app installations in your enterprise's organizations](https://docs.github.com/en/enterprise-cloud@latest/admin/managing-github-apps-for-your-enterprise/automate-installations).
Enterprise installations cannot read organization repositories, so the installer app
installs the inventory app on each organization, and stats are gathered with
per-organization installation tokens.

1. **Installer app** – create under *Enterprise settings → GitHub Apps* with webhooks
   disabled and the enterprise permission **Enterprise organization installations:
   Read and write**. Install it on the enterprise account.
2. **Inventory app** – create under the enterprise with webhooks disabled and read-only
   permissions: repository **Administration**, **Contents**, **Issues**, **Metadata**,
   **Pull requests**; organization **Administration**. Do not install it manually; the
   workflow installs it on every organization with access to all repositories.
3. Configure this repository:

   | Name | Type | Value |
   | --- | --- | --- |
   | `INSTALLER_APP_CLIENT_ID` | Variable | Installer app client ID |
   | `INSTALLER_APP_PRIVATE_KEY` | Secret | Installer app private key |
   | `INVENTORY_APP_ID` | Variable | Inventory app ID (numeric) |
   | `INVENTORY_APP_CLIENT_ID` | Variable | Inventory app client ID |
   | `INVENTORY_APP_PRIVATE_KEY` | Secret | Inventory app private key |

### `pat`

Uses the `ENTERPRISE_PAT` secret: a personal access token (classic) from an enterprise owner
with access to every organization. Use this where enterprise
GitHub App installations are unavailable (for example, older GHES versions).
