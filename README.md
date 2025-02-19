GitHub Action Templates for Ansible Roles
=========================================

This repository contains reusable GitHub Action templates to automate the CI process and release Ansible roles to Ansible Galaxy.

Workflows
---------

### 1. CI Workflow (`ci.yml`)

The **CI workflow** runs linting checks and Molecule tests to ensure the role meets best practices.

#### **Inputs**
| Name                | Description                                     | Required | Default |
|---------------------|-------------------------------------------------|----------|---------|
| `lint_path`        | The root path where the linters should evaluate | ❌       | `"."`   |
| `extra_molecule_args` | Extra user-provided arguments for Molecule   | ❌       | `""`    |

#### **Jobs**

- **Lint**: Runs `yamllint` and `ansible-lint` on the role.
- **Molecule**: Runs [Molecule](https://ansible.readthedocs.io/projects/molecule/) tests (Docker-based).

#### **Usage**

To use this workflow in another repository:
```yaml
jobs:
  test:
    uses: owner/repository/.github/workflows/ci.yml@main
    with:
      lint_path: "."
      extra_molecule_args: "--scenario-name default"
```


### 2. Release Workflow (`release.yml`)

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
