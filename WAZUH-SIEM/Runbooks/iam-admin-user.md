---
runbook:
  id: wazuh-iam-admin-user
  title: Wazuh Admin User Provisioning (OpenSearch Security)
  version: "1.1"

  navigation:
    breadcrumbs:
      - label: Home
        path: /
      - label: WAZUH-SIEM
        path: /WAZUH-SIEM
      - label: Runbooks
        path: /WAZUH-SIEM/Runbooks
      - label: IAM Admin User
        path: /WAZUH-SIEM/Runbooks/iam-admin-user.md

  scope:
    repo: home-network-lab
    project_folder: WAZUH-SIEM

  environment:
    os: Ubuntu Linux
    deployment: wazuh-docker single-node
    components:
      - wazuh-manager
      - wazuh-indexer (OpenSearch)
      - wazuh-dashboard

  objective: >
    Provision a full-privilege Wazuh Dashboard administrative user using
    OpenSearch Security as the authoritative identity and access management
    layer. Wazuh Dashboard UI user objects are optional and non-authoritative.

  authoritative_source:
    authentication: OpenSearch Security
    authorization: OpenSearch role mappings

principles:
  - OpenSearch Security is authoritative for authentication and authorization
  - Backend roles represent logical group membership only
  - Privileges are granted exclusively through role mappings
  - Wazuh Dashboard UI does not manage credentials
  - Do not store passwords or hashes in source control
  - Built-in admin account is reserved for break-glass use only

identity_model:
  admin_user:
    username: HacksonJackson
    type: internal_user
    backend_roles:
      - wazuh_admin  # logical group label, not a permission by itself
    privileges_granted_by:
      role_mapping: all_access
      via_backend_role: wazuh_admin

prerequisites:
  - Indexer container is running (single-node-wazuh.indexer-1)
  - Admin TLS certificates exist inside the indexer container:
      - /usr/share/wazuh-indexer/config/certs/root-ca.pem
      - /usr/share/wazuh-indexer/config/certs/admin.pem
      - /usr/share/wazuh-indexer/config/certs/admin-key.pem
  - all_access role mapping includes backend role wazuh_admin

procedure:
  - step: Enter indexer container
    command: docker exec -it single-node-wazuh.indexer-1 bash

  - step: Create or update internal admin user
    notes:
      - Password is entered locally and never written to disk
      - API field "password" expects plaintext input
      - Use "hash" only when explicitly providing a bcrypt hash
    command: |
      read -s -p "New password for HacksonJackson: " NEWPASS; echo
      curl -sk -X PUT \
        --cacert /usr/share/wazuh-indexer/config/certs/root-ca.pem \
        --cert  /usr/share/wazuh-indexer/config/certs/admin.pem \
        --key   /usr/share/wazuh-indexer/config/certs/admin-key.pem \
        https://localhost:9200/_plugins/_security/api/internalusers/HacksonJackson \
        -H "Content-Type: application/json" \
        -d "{\"password\":\"${NEWPASS}\",\"backend_roles\":[\"wazuh_admin\"],\"attributes\":{\"description\":\"Wazuh Dashboard admin\"}}"
      unset NEWPASS
    expected:
      http_code: [200, 201]

  - step: Validate authentication and effective roles (no UI involved)
    command: |
      curl -sk -u HacksonJackson \
        https://localhost:9200/_plugins/_security/api/account \
        -w "\nHTTP:%{http_code}\n" | head -n 80
    expected:
      http_code: 200
      roles_include:
        - all_access

  - step: Verify admin privilege via role mapping (authoritative)
    command: |
      curl -sk \
        --cacert /usr/share/wazuh-indexer/config/certs/root-ca.pem \
        --cert  /usr/share/wazuh-indexer/config/certs/admin.pem \
        --key   /usr/share/wazuh-indexer/config/certs/admin-key.pem \
        https://localhost:9200/_plugins/_security/api/rolesmapping/all_access
    expected:
      backend_roles_include:
        - wazuh_admin

  - step: Restart dashboard to clear cached security context
    rationale: Dashboard caches authorization state and must be restarted after IAM changes
    command: docker restart single-node-wazuh.dashboard-1

dashboard_notes:
  ui_user_required: false
  guidance:
    - Dashboard UI users do not create or manage OpenSearch credentials
    - UI-side users are optional metadata only
    - Authorization failures should always be debugged at the OpenSearch layer first

security_controls:
  - Store administrative credentials in a password manager
  - Avoid committing passwords, hashes, or API keys to version control
  - Apply least privilege for non-admin users (e.g., SOC analyst roles)
  - Use named admin accounts for daily operations

verification:
  success_criteria:
    - OpenSearch account endpoint returns HTTP 200 for the admin user
    - Effective roles include all_access
    - Wazuh Dashboard login succeeds for the admin user

non_goals:
  - Managing credentials through the Wazuh Dashboard UI
  - Using UI roles as the source of authorization
  - Replacing enterprise IdP solutions (LDAP, SAML, OIDC)
