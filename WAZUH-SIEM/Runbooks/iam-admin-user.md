[Home](/) • [WAZUH-SIEM](/WAZUH-SIEM) • [Runbooks](/WAZUH-SIEM/Runbooks)

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
      - wazuh-indexer
      - wazuh-dashboard

  objective: >
    Provision a full-privilege Wazuh Dashboard administrative user using
    OpenSearch Security as the authoritative identity and access management layer.
    Dashboard UI user objects are optional and non-authoritative.

  authoritative_source:
    authentication: OpenSearch Security
    authorization: OpenSearch role mappings

principles:
  - OpenSearch Security is authoritative for authentication and authorization
  - Backend roles represent logical group membership only
  - Privileges are granted exclusively through role mappings
  - Dashboard UI does not manage credentials
  - Built-in admin account is break-glass only

identity_model:
  admin_user:
    username: HacksonJackson
    type: internal_user
    backend_roles:
      - wazuh_admin
    privileges_granted_by:
      role_mapping: all_access

prerequisites:
  - Indexer container running: single-node-wazuh.indexer-1
  - Admin TLS certs present inside indexer container
  - all_access role mapping includes wazuh_admin

procedure:
  - step: Enter indexer container
    command: docker exec -it single-node-wazuh.indexer-1 bash

  - step: Create or update admin user
    notes:
      - Password entered locally, never stored
      - API field "password" expects plaintext
      - Use "hash" only for bcrypt input
    command: |
      read -s -p "New password: " NEWPASS; echo
      curl -sk -X PUT \
        --cacert /usr/share/wazuh-indexer/config/certs/root-ca.pem \
        --cert  /usr/share/wazuh-indexer/config/certs/admin.pem \
        --key   /usr/share/wazuh-indexer/config/certs/admin-key.pem \
        https://localhost:9200/_plugins/_security/api/internalusers/HacksonJackson \
        -H "Content-Type: application/json" \
        -d "{\"password\":\"${NEWPASS}\",\"backend_roles\":[\"wazuh_admin\"]}"
      unset NEWPASS
    expected_http: [200, 201]

  - step: Validate authentication
    command: |
      curl -sk -u HacksonJackson \
        https://localhost:9200/_plugins/_security/api/account
    expected_http: 200
    expected_roles:
      - all_access

  - step: Restart dashboard
    command: docker restart single-node-wazuh.dashboard-1

verification:
  success_criteria:
    - Authentication succeeds
    - all_access role applied
    - Dashboard login successful
