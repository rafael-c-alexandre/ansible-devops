GitHub Action Templates for Ansible
====================================

This repository contains reusable GitHub Action templates to automate the CI/CD process of Ansible playbooks and roles.

Actions
---------

### 1. Playbook-test action (`playbook-test/action.yml`)

The **Playbook-test action** runs a playbook passed as a parameter to the template, including some other customer properties.

#### **Inputs**
| Name                | Description                                     | Required | Default |
|---------------------|-------------------------------------------------|----------|---------|
| `playbook_path`     | The playbook file path                          | ❌       | `"."`   |
| `ansible_cfg_path`  | The ansible.cfg file path                       | ✅       | `""`    |
| `inventory_path`    | The inventory file path                         | ✅       | `""`    |
| `config_path`       | The config.yml file path                        | ✅       | `""`    |
| `run_idempotence_check`       | Whether the idempotence check should run or not      | ❌       | `"true"`    |

#### **Jobs**

- **Set up environment**: Copies all the config file to a common location.
- **Check playbook syntax**: Tests the playbook syntax.
- **Run playbook**: Runs the full playbook.
- **Check playbook idempotency**: Runs the playbook again to verify its idempotency.

#### **Usage**

To use this workflow in another repository:
```yaml
jobs:
  test:
    uses: rafael-c-alexandre/ansible-devops/.github/actions/playbook-test@main
    with:
      playbook_path: "./main.yml"
      ansible_cfg_path: "./ansible.cfg"
      inventory_path: "./inventory.ini"
      config_path: "./config.yml"
      run_idempotence_check: "true"
```

### 2. Start-ssh-server action (`start-ssh-server/action.yml`)

The **Start-ssh-server action** installs starts up an ssh server and checks if it is reachable.

#### **Inputs**
| Name                | Description                                     | Required | Default |
|---------------------|-------------------------------------------------|----------|---------|
| `ssh_private_key_path`| The SSH private key file path                 | ✅       | `""`   |
| `ssh_public_key_path` | The SSH public key file path                  | ✅       | `""`    |

#### **Jobs**

- **Install OpenSSH**: Install OpenSSH service and its dependencies.
- **Start SSH server**: Starts an SSH server.
- **Add custom SSH keys**: Adds custom SSH keys based on the parameters provided.
- **Test SSH connectivity**: Checks if the started SSH server is reachable.

#### **Usage**

To use this workflow in another repository:
```yaml
jobs:
  test:
    uses: rafael-c-alexandre/ansible-devops/.github/actions/start-ssh-server@main
      with:
        ssh_private_key_path: "./keys/ci"
        ssh_public_key_path: "./keys/ci.pub"
```

Workflows
---------

### 1. Lint Workflow (`workflows/lint.yml`)

The **Lint workflow** runs linting checks to ensure the role meets best practices.

#### **Inputs**
| Name                | Description                                     | Required | Default |
|---------------------|-------------------------------------------------|----------|---------|
| `lint_path`        | The root path where the linters should evaluate | ❌       | `"."`   |

#### **Jobs**

- **Lint**: Runs `yamllint` and `ansible-lint` on the role.

#### **Usage**

To use this workflow in another repository:
```yaml
jobs:
  test:
    uses: owner/repository/.github/workflows/lint.yml@main
    with:
      lint_path: "."
```

### 2. Molecule Workflow (`workflows/molecule.yml`)

The **Molecule workflow** runs Molecule tests to ensure the role meets best practices.

#### **Inputs**
| Name                | Description                                     | Required | Default |
|---------------------|-------------------------------------------------|----------|---------|
| `extra_molecule_args` | Extra user-provided arguments for Molecule   | ❌       | `""`    |

#### **Jobs**

- **Molecule**: Runs [Molecule](https://ansible.readthedocs.io/projects/molecule/) tests (Docker-based).

#### **Usage**

To use this workflow in another repository:
```yaml
jobs:
  test:
    uses: owner/repository/.github/workflows/molecule.yml@main
    with:
      extra_molecule_args: "--scenario-name default"
```

### 3. Release Workflow (`workflows/release.yml`)

The **Release workflow** publishes the role to Ansible Galaxy.

#### **Secrets**

The following secrets need to be stored in the downstream github actions repo that use these templates.

| Name         | Description                                  | Required |
|-------------|----------------------------------------------|----------|
| `GALAXY_PAT` | The API token for Ansible Galaxy authentication | ✅       |

#### **Jobs**

- **Release**: Imports the Ansible role to Ansible Galaxy using `ansible-galaxy role import`.

#### **Usage**

To use this workflow in another repository:
```yaml
jobs:
  publish:
    uses: rafael-c-alexandre/ansible-roles-devops/.github/workflows/release.yml@main
    secrets:
      GALAXY_PAT: ${{ secrets.GALAXY_PAT }}
```

---

License
-------

MIT
