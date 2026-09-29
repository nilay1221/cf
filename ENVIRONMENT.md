# `cf` environment variables

This is the environment-variable reference for the `cf` CLI. The tables cover
35 supported names, including three deprecated aliases, read by `cf` itself
or by shared helpers in workflows that `cf` owns. A framework launched by
`cf dev` or `cf build` may recognize additional
variables; consult that framework's documentation for those settings.

Use `cloudflare.config.ts` for project defaults where an equivalent exists,
flags for a single invocation, and environment variables for process-specific
values and secrets. The tables below say when there is no equivalent.

## Account, zone, and API

| Variable                       | Effect                                                                                                                                                           | Flag or project equivalent                                                                  |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| `CLOUDFLARE_ACCOUNT_ID`        | Selects the account for API requests.                                                                                                                            | `accountId` in `cloudflare.config.ts`; no account flag.                                     |
| `CLOUDFLARE_ZONE_ID`           | Default zone ID or domain name for commands that need a zone.                                                                                                    | A zone positional argument or global `--zone` / `-z`.                                       |
| `CLOUDFLARE_COMPLIANCE_REGION` | Selects `public` or `fedramp_high` for API and auth requests.                                                                                                    | `complianceRegion: "public"` or `"fedramp-high"` in `cloudflare.config.ts`; no region flag. |
| `CLOUDFLARE_API_TOKEN`         | Scoped API token, preferred over a stored OAuth profile.                                                                                                         | `cf auth login` for OAuth; no token flag or project setting.                                |
| `CLOUDFLARE_API_BASE_URL`      | Overrides the Cloudflare API URL, including the URL that compliance region and API environment would otherwise choose. Intended for testing or custom endpoints. | None.                                                                                       |
| `WRANGLER_API_ENVIRONMENT`     | `production` (default) or `staging` for Cloudflare API and OAuth endpoints. The name is retained for compatibility.                                              | None; `--mode` selects project configuration and dotenv files, **not** the API environment. |
| `CF_ACCOUNT_ID`                | Deprecated account-ID spelling read by the shared account picker.                                                                                                | Use `CLOUDFLARE_ACCOUNT_ID` or project `accountId`.                                         |
| `CF_API_TOKEN`                 | Deprecated token spelling.                                                                                                                                       | Use `CLOUDFLARE_API_TOKEN`.                                                                 |
| `CF_API_BASE_URL`              | Deprecated API-base-URL spelling.                                                                                                                                | Use `CLOUDFLARE_API_BASE_URL`.                                                              |

The three `CF_*` spellings above still work as process environment variables
through shared helpers and emit deprecation warnings. They are not loaded from
`.env` files. `CF_ACCOUNT_ID` is only consulted after `cf` has checked
`CLOUDFLARE_ACCOUNT_ID` and project settings; use the current spelling to avoid
that different priority.

## Authentication and Access

| Variable                          | Effect                                                                                                                                                 | Flag or project equivalent       |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------- |
| `CLOUDFLARE_ACCESS_CLIENT_ID`     | Cloudflare Access service-token client ID for an Access-protected auth endpoint.                                                                       | None. Use with the secret below. |
| `CLOUDFLARE_ACCESS_CLIENT_SECRET` | Matching Access service-token secret.                                                                                                                  | None.                            |
| `CLOUDFLARE_AUTH_USE_KEYRING`     | `true` forces OS-keychain storage for OAuth credentials; `false` forces file storage. An unavailable keychain causes an error when explicitly enabled. | None in `cf`.                    |
| `CLOUDFLARE_CLIENT_ID`            | Overrides the registered OAuth client ID. Advanced auth setup only.                                                                                    | None.                            |
| `WRANGLER_CF_AUTHORIZATION_TOKEN` | Supplies an existing Cloudflare Access authorization cookie for an Access-protected OAuth endpoint. Advanced auth setup only.                          | None.                            |
| `WRANGLER_AUTH_DOMAIN`            | Overrides the OAuth auth domain.                                                                                                                       | None. Usually leave unset.       |
| `WRANGLER_AUTH_URL`               | Overrides the OAuth authorization URL.                                                                                                                 | None. Usually leave unset.       |
| `WRANGLER_TOKEN_URL`              | Overrides the OAuth token URL.                                                                                                                         | None. Usually leave unset.       |
| `WRANGLER_REVOKE_URL`             | Overrides the OAuth revocation URL.                                                                                                                    | None. Usually leave unset.       |

These `WRANGLER_*` names are compatibility settings in the shared OAuth
implementation used by `cf`. They do not make `cf` read Wrangler's stored
credentials.

## Local tools, tunnels, and containers

| Variable                        | Effect                                                                                | Flag or project equivalent                             |
| ------------------------------- | ------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| `CLOUDFLARE_REGISTRY_PATH`      | Directory for the local dev registry shared with compatible peers.                    | None.                                                  |
| `CLOUDFLARED_PATH`              | Path to a `cloudflared` executable for Tunnel and Access commands.                    | None.                                                  |
| `TUNNEL_MANAGEMENT_TOKEN`       | Existing management token for `cf tunnels tail`; cannot be combined with a tunnel ID. | Give a tunnel ID to have `cf` request a token instead. |
| `CLOUDFLARE_CONTAINER_REGISTRY` | Overrides the managed Container image registry hostname.                              | None.                                                  |
| `WRANGLER_DOCKER_BIN`           | Path to the Docker executable used by Container workflows.                            | None.                                                  |
| `WRANGLER_DOCKER_HOST`          | Docker endpoint for Container workflows; takes precedence over `DOCKER_HOST`.         | None.                                                  |
| `DOCKER_HOST`                   | Standard Docker endpoint, used when `WRANGLER_DOCKER_HOST` is unset.                  | None.                                                  |

## Telemetry, output, and diagnostics

| Variable                | Effect                                                                                                                                     | Flag or project equivalent                                                               |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------- |
| `CF_SEND_TELEMETRY`     | `true` / `1` enables or `false` / `0` disables `cf` telemetry for this invocation.                                                         | `cf cli telemetry enable`, `disable`, or `status` manages the stored machine preference. |
| `WRANGLER_SEND_METRICS` | Compatibility fallback for telemetry when `CF_SEND_TELEMETRY` is unset or invalid. Accepts the same values.                                | Prefer `CF_SEND_TELEMETRY`.                                                              |
| `DO_NOT_TRACK`          | `1` or `true` disables telemetry regardless of other preferences.                                                                          | None.                                                                                    |
| `CF_QUIET`              | `1` disables the animated spinner and external terminal progress.                                                                          | `--quiet` also suppresses nonessential CLI output; its scope is wider.                   |
| `CF_NO_OSC_PROGRESS`    | `1` disables OSC 9;4 terminal progress.                                                                                                    | None.                                                                                    |
| `CF_FORCE_OSC_PROGRESS` | `1` enables OSC 9;4 progress on an unrecognized compatible terminal; still requires a TTY and is overridden by the disable/quiet settings. | None.                                                                                    |
| `NO_COLOR`              | Any value disables `cf` styling.                                                                                                           | None.                                                                                    |
| `FORCE_COLOR`           | A value other than `0` enables styling when `NO_COLOR` is unset.                                                                           | None.                                                                                    |
| `DEBUG`                 | Any nonempty value enables additional diagnostic output, including sanitized error stacks and telemetry payloads.                          | None.                                                                                    |
| `WRANGLER_LOG_SANITIZE` | `true` (default) omits deploy-helper request and response details; `false` includes them when `DEBUG` is set. They may contain secrets.    | None.                                                                                    |

See the [telemetry policy](packages/cli/telemetry.md) for the data sent and how the stored
preference works.

## Security

- Put API and Access tokens in your CI secret store or another protected
  environment source. Do not commit tokens in `.env` files; ignore local secret
  files in Git.
- Exported process variables can be inherited by child processes. `cf` limits
  inheritance of file-sourced values during its own delegated workflows, but
  that does not change values you exported before starting `cf`.
- `DEBUG` can print diagnostic data. In particular,
  `WRANGLER_LOG_SANITIZE=false` may reveal request or response payloads in
  deploy logs. Review logs before sharing them.
- API-base-URL and OAuth-endpoint overrides can send credentials to the
  configured endpoint. Use only endpoints you trust.
- `CLOUDFLARE_AUTH_USE_KEYRING=false` forces OAuth credentials into the
  local file store rather than the OS keychain.

## Precedence and `.env` files

There is no universal "flags > environment > config" rule. Resolution depends
on the value:

| Value             | Highest to lowest priority                                                                                                                                                   |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Account           | `CLOUDFLARE_ACCOUNT_ID` → project `accountId` → deprecated `CF_ACCOUNT_ID` through the shared auth helper → cached account → single-account selection or interactive picker. |
| Zone              | Command positional zone → `--zone` / `-z` → `CLOUDFLARE_ZONE_ID`.                                                                                                            |
| Compliance region | `CLOUDFLARE_COMPLIANCE_REGION` → project `complianceRegion` → `public`.                                                                                                      |
| API credential    | `CLOUDFLARE_API_TOKEN` → deprecated `CF_API_TOKEN` → selected OAuth profile. `--profile` chooses an OAuth profile only when an environment token is not in use.              |
| API base URL      | `CLOUDFLARE_API_BASE_URL` → deprecated `CF_API_BASE_URL` → URL derived from compliance region and `WRANGLER_API_ENVIRONMENT`.                                                |
| Telemetry         | `DO_NOT_TRACK` opt-out → valid `CF_SEND_TELEMETRY` → valid `WRANGLER_SEND_METRICS` → stored preference → enabled by default.                                                 |

For normal API commands, `cf` reads only these names from project `.env`
files: `CLOUDFLARE_ACCESS_CLIENT_ID`,
`CLOUDFLARE_ACCESS_CLIENT_SECRET`, `CLOUDFLARE_ACCOUNT_ID`,
`CLOUDFLARE_API_TOKEN`, `CLOUDFLARE_COMPLIANCE_REGION`,
`CLOUDFLARE_ZONE_ID`, and `WRANGLER_API_ENVIRONMENT`. Existing process values
win over files. File priority rises through `.env`, `.env.local`, then (when
`--mode <name>` is given) `.env.<name>` and `.env.<name>.local`.

File values are applied temporarily and removed after the command. `--local`
does not load them. Commands that launch another process, including `cf dev`
and `cf build`, do not pass file-sourced credentials to that process by
default. Deployment workflows load file values for their API phase after the
build. Values explicitly exported in the invoking process retain normal child
process inheritance. Other variables in this reference, including
`CLOUDFLARE_API_BASE_URL` and telemetry settings, must be set in the process
environment rather than in `.env` for `cf` to read them.

`--mode` has no environment-variable equivalent. It selects the mode passed
to a function-form `cloudflare.config.ts` export and the mode-specific dotenv
files. It does not replace `WRANGLER_API_ENVIRONMENT`.

## Examples

These examples use POSIX shell syntax; PowerShell uses
`$env:CLOUDFLARE_ACCOUNT_ID = "..."` and equivalent assignments for other
variables. On Windows Command Prompt, use `set NAME=value` for the current
session. Prefer your CI system's secret store for tokens.

```sh
# Account and zone; an explicit zone argument or --zone wins over the default.
export CLOUDFLARE_ACCOUNT_ID="<account-id>"
export CLOUDFLARE_ZONE_ID=example.com
cf dns records list
cf dns records list --zone other.example.com

# Select a project-config/dotenv mode; this does not switch the API to staging.
cf d1 list --mode staging

# In CI, provide a scoped token through the secret store and an account ID.
# The token value should not be committed to the repository.
export CLOUDFLARE_API_TOKEN="<token-from-secret-store>"
cf d1 list

# Where a destructive command offers --force, pass it explicitly in CI.
# Check that command's --help for its confirmation option.
```

Without a TTY or in CI, `cf` cannot prompt to choose among multiple accounts
or request missing values. Supply the account, token, required command
arguments, and `--force` where the command requires destructive confirmation.
`CI` detection also comes from common CI environment signals; `CI=1` is not a
substitute for those required values. OAuth login is interactive, so a scoped
API token is the usual non-interactive credential.

## Migrating from Wrangler

- Replace `CF_API_TOKEN`, `CF_ACCOUNT_ID`, and `CF_API_BASE_URL` with their
  `CLOUDFLARE_*` spellings. The old names are recognized only as deprecated
  process-environment fallbacks.
- Replace a global API key/email pair (`CLOUDFLARE_API_KEY` and
  `CLOUDFLARE_EMAIL`, or `CF_API_KEY` and `CF_EMAIL`) with a scoped
  `CLOUDFLARE_API_TOKEN` or `cf auth login`. `cf` does not accept global-key
  authentication.
- Do not use `CLOUDFLARE_ENV` or Wrangler's `--env` to select a `cf` project
  mode. Use `cf --mode <name> ...` with a function-form
  `cloudflare.config.ts` and, if needed, `.env.<name>` files.
- Use `accountId` and `complianceRegion` in `cloudflare.config.ts` for project
  defaults, `--zone` or `CLOUDFLARE_ZONE_ID` for zones, and `--profile` for a
  named OAuth profile. There is no `--account-id`, `--api-token`, or
  `--compliance-region` global flag.
- `WRANGLER_API_ENVIRONMENT` remains recognized for staging API access. It
  has no `CLOUDFLARE_*` replacement today. `WRANGLER_SEND_METRICS` remains a
  telemetry fallback; prefer `CF_SEND_TELEMETRY` for `cf`.

`CF_HIDE_COMMANDS` and `CF_NO_PROMPT` are not supported `cf` runtime
settings. Hidden generated commands are registered regardless of
`CF_HIDE_COMMANDS`, and non-interactive behavior follows TTY/CI detection.

## Scope of this reference

The tables list settings for `cf` and the shared helpers it invokes. `cf`
also observes ambient variables from the OS, Node.js, package managers, CI,
and terminals (for example, `HOME`, `XDG_CONFIG_HOME`, `APPDATA`, `PATH`,
`NODE_OPTIONS`, `CI`, `TERM_PROGRAM`, and `WT_SESSION`). These determine
platform behavior and are not `cf` configuration options. Coding-agent
detection observes 22 agent-provided signal names; those are inputs from the
agent harness, not settings users need to configure.

Internal handoff values such as `CF_DELEGATION`, `CLOUDFLARE_CF_AUTH`,
`CLOUDFLARE_PREVIEW_BUILD`, `CLOUDFLARE_VITE_FORCE_BUILD_OUTPUT`,
`SPARROW_SOURCE_KEY`, and `CLI_VERSION` are not public configuration options.
Variables consumed solely by tools launched from `cf dev`, `cf build`, or
`cf migrate` belong to those tools' documentation.
