# enterprise-repository-inventory
Collects repository statistics for every organization in a GitHub Enterprise using
[gh-repo-stats-plus](https://github.com/mona-actions/gh-repo-stats-plus), via the
`All Enterprise Organization Repository Stats` workflow.

## Authentication

The workflow's `auth` input selects how it authenticates.

### `github-app` (default)

Uses a single GitHub App owned by the enterprise, based on
[Automating app installations in your enterprise's organizations](https://docs.github.com/en/enterprise-cloud@latest/admin/managing-github-apps-for-your-enterprise/automate-installations).
An enterprise installation only receives the app's enterprise permissions and cannot read
organization repositories, so the workflow uses it to install the same app on every
organization, then gathers stats with per-organization installation tokens.

1. Create the app under *Enterprise settings → GitHub Apps* with webhooks disabled and
   these permissions:
   - Enterprise: **Enterprise organization installations** – Read and write
   - Organization: **Administration** – Read
   - Repository: **Administration**, **Contents**, **Issues**, **Metadata**,
     **Pull requests** – Read
2. Install the app on the enterprise account. The workflow installs it on each
   organization (with access to all repositories) on its first run.
3. Configure this repository:

   | Name | Type | Value |
   | --- | --- | --- |
   | `APP_ID` | Variable | App ID (numeric) |
   | `APP_CLIENT_ID` | Variable | Client ID |
   | `APP_PRIVATE_KEY` | Secret | Private key |

The app's private key can both install apps across the enterprise and read every
repository, so store it accordingly.

### `pat`

Uses the `ENTERPRISE_PAT` secret: a personal access token (classic) from an enterprise owner
with access to every organization. Use this where enterprise
GitHub App installations are unavailable (for example, older GHES versions).

## GHE.com (data residency) and GHES

- Set the `base_url` input to the API URL, for example `https://api.SUBDOMAIN.ghe.com`
  or `https://ghes.example.com/api/v3`.
- `gh-repo-stats-plus` installs its CLI extensions from github.com, and the workflow's
  `GITHUB_TOKEN` is not valid there. Add a `GITHUB_COM_TOKEN` secret containing a
  github.com token, for example a fine-grained PAT with read-only access to public
  repositories. On github.com this secret is not needed.
