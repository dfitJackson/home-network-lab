---
runbook:
  id: wazuh-iam-admin-user
  title: Wazuh Admin User Provisioning (OpenSearch Security)
  version: "1.0"
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
    Provision a full-privilege Wazuh Dashboard admin user using OpenSearch Security
    as the source of truth. Dashboard UI user objects are optional.

principles:
  - OpenSearch Security is authoritative for authn/authz
  - Backend roles represent group membership
  - Role mappings grant privileges
  - Do not store passwords or hashes in source control
  - Use built-in admin for break-glass only

identity_model:
  admin_user:
    username: HacksonJackson
    type: internal_user
    backend_roles:
      - wazuh_admin
    privileges_granted_by:
      role_mapping: all_access
      via_backend_role: wazuh_admin

prerequisites:
  - Indexer container is running (single-node-wazuh.indexer-1)
  - Admin TLS certs exist inside the indexer container:
      - /usr/share/wazuh-indexer/config/certs/root-ca.pem
      - /usr/share/wazuh-indexer/config/certs/admin.pem
      - /usr/share/wazuh-indexer/config/certs/admin-key.pem
  - all_access role mapping includes backend role wazuh_admin

procedure:
  - step: Enter indexer container
    command: docker exec -it single-node-wazuh.indexer-1 bash

  - step: Create or update internal user (password prompted locally)
    notes:
      - Password is entered locally and never written to disk
      - API field "password" expects plaintext
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

  - step: Verify role mapping grants admin via backend role (authoritative)
    command: |
      curl -sk \
        --cacert /usr/share/wazuh-indexer/config/certs/root-ca.pem \
        --cert  /usr/share/wazuh-indexer/config/certs/admin.pem \
        --key   /usr/share/wazuh-indexer/config/certs/admin-key.pem \
        https://localhost:9200/_plugins/_security/api/rolesmapping/all_access
    expected:
      backend_roles_include:
        - wazuh_admin

  - step: Restart dashboard to clear cached auth state
    command: docker restart single-node-wazuh.dashboard-1

dashboard_notes:
  ui_user_required: false
  guidance:
    - If you create a user in the Dashboard UI, it does not create credentials in OpenSearch
    - Treat UI-side user objects as optional metadata only

security_controls:
  - Store admin password in a password manager
  - Avoid committing secrets (passwords, hashes, API keys)
  - Use least privilege for non-admin users (separate analyst role)

verification:
  success_criteria:
    - OpenSearch account endpoint returns HTTP 200 for the admin user
    - Effective roles include all_access
    - Dashboard login succeeds for the admin user

