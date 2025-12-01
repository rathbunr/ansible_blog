---
date: 2025-11-28
categories:
  - Ansible
  - Windows
tags:
  - ansible
  - active-directory
  - ldap
  - windows
authors:
  - robert
---

# Dynamic Inventory from Active Directory with microsoft.ad.ldap

Need to automatically populate your Ansible inventory from Active Directory? 
The `microsoft.ad.ldap` plugin makes this straightforward - once you know 
the gotchas.

<!-- more -->

## Prerequisites

Your execution environment needs the `microsoft.ad` collection:

```yaml title="execution-environment.yml (snippet)"
dependencies:
  galaxy:
    collections:
      - name: microsoft.ad
        version: "1.7.1"
```

## The Inventory File

The filename **must** be `microsoft.ad.ldap.yml` or `microsoft.ad.ldap.yaml`:

```yaml title="inventories/microsoft.ad.ldap.yml"
plugin: microsoft.ad.ldap
server: ldaps://dc01.example.com
port: 636
tls_mode: ldaps

auth_protocol: simple
username: svc_ansible@example.com
password: "{{ lookup('env', 'AD_PASSWORD') }}"

search_base: OU=Servers,DC=example,DC=com
search_scope: subtree

filter: >-
  (&
    (objectClass=computer)
    (operatingSystem=*Server*)
    (!(userAccountControl:1.2.840.113556.1.4.803:=2))
  )

attributes:
  computer: dNSHostName
  os: operatingSystem

# Use FQDN as inventory hostname
inventory_hostname: attributes.dNSHostName
```

!!! note "The Filter Explained"
    - `objectClass=computer` - Only computer objects
    - `operatingSystem=*Server*` - Only servers (excludes workstations)
    - The `userAccountControl` bit mask excludes disabled accounts

## Grouping by Operating System

Add group mappings based on attributes:

```yaml title="inventories/microsoft.ad.ldap.yml (continued)"
groups:
  windows_2019: >-
    'Windows Server 2019' in (attributes.operatingSystem | default(''))
  windows_2022: >-
    'Windows Server 2022' in (attributes.operatingSystem | default(''))
  rhel: >-
    'Red Hat' in (attributes.operatingSystem | default(''))
```

## Testing Locally

Before setting up in AAP, test with the CLI:

=== "List all hosts"
    ```bash
    ansible-inventory -i inventories/microsoft.ad.ldap.yml --list
    ```

=== "Show specific host"
    ```bash
    ansible-inventory -i inventories/microsoft.ad.ldap.yml --host server01.example.com
    ```

=== "Graph view"
    ```bash
    ansible-inventory -i inventories/microsoft.ad.ldap.yml --graph
    ```

## Common Issues

### Certificate Verification Fails

If using LDAPS with an internal CA:

```yaml
tls_mode: ldaps
ca_cert: /etc/pki/tls/certs/internal-ca.pem
```

### Hosts Show Hostname Instead of FQDN

Ensure you're using `dNSHostName` not `name`:

```yaml
# Wrong - gives short hostname
inventory_hostname: attributes.name

# Correct - gives FQDN
inventory_hostname: attributes.dNSHostName
```

### Plugin Not Found in AAP

The default `awx-ee` doesn't include `microsoft.ad`. Build a custom EE 
or use one that includes it.

## Setting Up in AAP

1. Create a **Source Control** credential for your inventory repo
2. Create a **Project** pointing to that repo
3. Create an **Inventory** with source type **Sourced from a Project**
4. Select your inventory file path

!!! tip "Sync Schedule"
    Set up a schedule to sync inventory regularly - AD changes won't 
    appear until the next sync.

---

*This pairs well with [dynamic inventory from ServiceNow](/posts/servicenow-inventory) 
for CMDB-driven automation.*
