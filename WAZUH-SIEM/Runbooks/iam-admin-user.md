[Home](../../README.md) · [Wazuh Runbooks](../Runbooks/)

# Wazuh Administrative User Provisioning

> **Runbook ID:** `wazuh-iam-admin-user`  
> **Version:** 2.0  
> **Environment status:** Operational—In Progress  
> **Last reviewed:** July 2026

## Purpose

This runbook documents how to provision and validate a full-privilege administrative user for a Docker-based Wazuh single-node lab.

It uses a generic example identity. Real usernames, passwords, client information, and credentials are intentionally excluded from this public repository.

## Environment

| Component | Current lab implementation |
|---|---|
| Host | Ubuntu Linux on Dell OptiPlex 7050 |
| Deployment | Wazuh Docker single-node |
| Wazuh version | 4.14.3 at time of review |
| Manager | `single-node-wazuh.manager-1` |
| Indexer | `single-node-wazuh.indexer-1` |
| Dashboard | `single-node-wazuh.dashboard-1` |
| Identity store | OpenSearch Security internal users |
| Authorization | OpenSearch Security role mappings |

The containers are running and the platform is accessible. Agent onboarding, log-source expansion, detection validation, and monitoring use cases remain in progress.

## Security Model

Wazuh Dashboard authentication in this deployment is backed by OpenSearch Security.

- **Internal users** provide authentication.
- **Backend roles** represent logical group membership.
- **Role mappings** grant permissions to backend roles or users.
- The example backend role in this runbook is `wazuh_admin`.
- The built-in `all_access` role grants full administrative access and should be tightly restricted.
- Built-in administrative credentials should be reserved for recovery and controlled administration.

Creating a user with a backend role does not grant permissions by itself. The backend role must also be included in the appropriate OpenSearch role mapping.

## Example Identity

| Field | Sanitized example |
|---|---|
| Username | `wazuh-lab-admin` |
| Backend role | `wazuh_admin` |
| OpenSearch role | `all_access` |
| Password | Entered interactively; never stored in GitHub |

## Prerequisites

Before making changes:

- Confirm all three Wazuh containers are running.
- Confirm the indexer health endpoint is reachable.
- Confirm the administrative TLS certificate and key are available inside the indexer container.
- Confirm `jq` is available inside the indexer container for safe JSON generation.
- Review the existing `all_access` role mapping before modifying users.
- Use an isolated, authorized lab environment.

Check the containers from the Ubuntu host:

```bash
docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}' |
  grep 'single-node-wazuh'
```

## Procedure

### 1. Enter the indexer container

```bash
docker exec -it single-node-wazuh.indexer-1 bash
```

### 2. Set non-secret variables

```bash
NEW_USER="wazuh-lab-admin"
BACKEND_ROLE="wazuh_admin"

CA_CERT="/usr/share/wazuh-indexer/config/certs/root-ca.pem"
ADMIN_CERT="/usr/share/wazuh-indexer/config/certs/admin.pem"
ADMIN_KEY="/usr/share/wazuh-indexer/config/certs/admin-key.pem"
```

### 3. Review the current administrative role mapping

```bash
curl --silent --show-error \
  --cacert "$CA_CERT" \
  --cert "$ADMIN_CERT" \
  --key "$ADMIN_KEY" \
  "https://localhost:9200/_plugins/_security/api/rolesmapping/all_access"
```

Verify that the mapping includes the intended backend role before continuing. Do not replace an existing role mapping blindly; an incomplete replacement can remove access for other administrators.

### 4. Create the user payload securely

Use an interactive prompt so the password is not written into shell history. `jq` safely encodes special characters into JSON.

```bash
umask 077
PAYLOAD_FILE="$(mktemp)"
trap 'rm -f "$PAYLOAD_FILE"; unset NEWPASS' EXIT

read -r -s -p "New password for ${NEW_USER}: " NEWPASS
echo

jq -n \
  --arg password "$NEWPASS" \
  --arg backend_role "$BACKEND_ROLE" \
  '{
    password: $password,
    backend_roles: [$backend_role]
  }' > "$PAYLOAD_FILE"

unset NEWPASS
```

### 5. Create or update the internal user

```bash
curl --silent --show-error \
  --request PUT \
  --cacert "$CA_CERT" \
  --cert "$ADMIN_CERT" \
  --key "$ADMIN_KEY" \
  --header "Content-Type: application/json" \
  --data-binary "@$PAYLOAD_FILE" \
  "https://localhost:9200/_plugins/_security/api/internalusers/${NEW_USER}"
```

Expected result: HTTP success with confirmation that the internal user was created or updated.

Remove the temporary payload immediately:

```bash
rm -f "$PAYLOAD_FILE"
trap - EXIT
```

### 6. Validate authentication

This command prompts for the new password rather than placing it on the command line:

```bash
curl --silent --show-error \
  --user "$NEW_USER" \
  --cacert "$CA_CERT" \
  "https://localhost:9200/_plugins/_security/api/account"
```

Confirm that:

- Authentication succeeds.
- The response identifies the expected user.
- The `wazuh_admin` backend role is present.
- The `all_access` role is applied through role mapping.

### 7. Validate Dashboard access

Sign in to the Wazuh Dashboard using the new lab administrator and confirm that administrative functions are available.

A container restart is not normally required for a user created through the OpenSearch Security API. Restart only when evidence shows that a component has stale state or another configuration change requires it.

## Success Criteria

The procedure is complete when:

- The internal user exists.
- Authentication succeeds without using built-in recovery credentials.
- The expected backend role is returned.
- The correct OpenSearch role is applied.
- Dashboard login succeeds.
- No password, payload file, token, or client-specific evidence remains in the repository or shell history.

## Troubleshooting

### HTTP 401 — Authentication failed

Check:

- Username spelling
- Whether the user was created successfully
- Password entry
- OpenSearch Security internal-user response

### HTTP 403 — Authenticated but unauthorized

Authentication is working, but authorization is incomplete. Review the backend role and the `all_access` role mapping.

### TLS validation failure

Confirm the certificate paths and certificate validity. Do not make `--insecure` or `-k` the permanent fix for a certificate problem.

### Dashboard login fails but the account endpoint succeeds

Treat this as a Dashboard or integration problem rather than recreating the user immediately. Review Dashboard logs and authentication configuration.

## Removal

If the sanitized example account is no longer required, remove it with an authorized administrative certificate:

```bash
curl --silent --show-error \
  --request DELETE \
  --cacert "$CA_CERT" \
  --cert "$ADMIN_CERT" \
  --key "$ADMIN_KEY" \
  "https://localhost:9200/_plugins/_security/api/internalusers/${NEW_USER}"
```

Validate that required administrative access still exists before ending the session.

## Documentation and Evidence Rules

Safe portfolio evidence can include:

- Sanitized API response structure
- Container-health output with sensitive values removed
- Role-mapping concepts
- Troubleshooting decisions
- Validation criteria

Do not publish:

- Real administrative usernames
- Passwords or password hashes
- API tokens
- Private keys or certificate contents
- Internal addresses that are not necessary to explain the lab
- Screenshots containing credentials or client information

---

[Home](../../README.md) · [Top](#wazuh-administrative-user-provisioning)
