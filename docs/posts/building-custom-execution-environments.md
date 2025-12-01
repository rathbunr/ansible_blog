---
date: 2025-11-30
categories:
  - Ansible
  - AAP
tags:
  - ansible
  - execution-environments
  - automation-platform
authors:
  - robert
---

# Building Custom Execution Environments for AAP 2.6

Execution Environments (EEs) are the foundation of Ansible Automation Platform. 
Here's how to build custom EEs that include the collections and dependencies 
your automation requires.

<!-- more -->

## Why Custom Execution Environments?

Out of the box, AAP provides minimal and supported execution environments. 
However, real-world automation typically requires:

- Additional Ansible collections (e.g., `microsoft.ad`, `community.general`)
- Python libraries for specific modules
- System packages like `git` or `curl`

## The Definition File

The `execution-environment.yml` file defines what goes into your EE:

```yaml title="execution-environment.yml"
---
version: 3

options:
  package_manager_path: /usr/bin/microdnf

images:
  base_image:
    name: registry.redhat.io/ansible-automation-platform-25/ee-minimal-rhel9:latest

dependencies:
  system:
    - git
    - curl
  python:
    - jmespath
    - requests
  galaxy:
    collections:
      - name: ansible.windows
        version: "2.5.0"
      - name: microsoft.ad
        version: "1.7.1"
      - name: community.general
        version: "9.5.0"
```

## Building the Image

With `ansible-builder` installed, building is straightforward:

```bash
ansible-builder build \
  --tag my-custom-ee:1.0.0 \
  --container-runtime podman
```

!!! tip "Use Podman"
    On RHEL systems, Podman is the default container runtime and works 
    seamlessly with `ansible-builder`.

## Pushing to a Registry

Once built, push to your private automation hub or container registry:

```bash
podman push my-custom-ee:1.0.0 \
  hub.example.com/ee/my-custom-ee:1.0.0
```

## Using in AAP

In Automation Controller, navigate to:

1. **Administration** → **Execution Environments**
2. Click **Add**
3. Provide the image path from your registry

!!! warning "Registry Authentication"
    Ensure your execution nodes have credentials configured to pull 
    from your private registry.

## Key Takeaways

- Start with `ee-minimal` and add only what you need
- Pin collection versions for reproducibility
- Automate the build process with CI/CD
- Tag images with meaningful versions (not just `latest`)

---

*Next up: Automating EE builds with GitHub Actions and AAP workflows.*
