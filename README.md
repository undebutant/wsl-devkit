# wsl-devkit

Script and Ansible roles to provision a convenient Ubuntu devkit on WSL2 :)


## What's inside

| Software             | Ansible variable               | Default version                 |
| -------------------- | ------------------------------ | ------------------------------- |
| ansible              | `n/a`                          | `latest` (pip)                  |
| ansible-lint         | `n/a`                          | `latest` (pip)                  |
| aws cli              | `awscli_version`               | `2.9.0`                         |
| curl                 | `n/a`                          | `latest`                        |
| docker               | `n/a`                          | `latest` (PPA)                  |
| docker-compose       | `docker_compose_version`       | `2.12.2`                        |
| editorconfig-checker | `editorconfig_checker_version` | `2.6.0`                         |
| gcloud               | `gcloud_version`               | `410.0.0`                       |
| git                  | `n/a`                          | `latest` (PPA)                  |
| git-lfs              | `n/a`                          | `latest` (PPA)                  |
| gnupg                | `n/a`                          | `latest`                        |
| go                   | `go_version`                   | `1.19.3`                        |
| helm                 | `helm_version`                 | `3.10.2`                        |
| helm diff            | `helm_diff_version`            | `3.6.0`                         |
| helmfile             | `helmfile_version`             | `0.148.1`                       |
| java                 | `openjdk_version`              | `19`                            |
| jq                   | `n/a`                          | `latest`                        |
| kubectl              | `kubernetes_version`           | `1.25.4`                        |
| make                 | `n/a`                          | `latest`                        |
| mongodb shell        | `mongodb_shell_version`        | `1.6.0`                         |
| nodejs               | `node_version`                 | `18`                            |
| openstack client     | `openstack_client_version`     | `6.0.0`                         |
| pip                  | `n/a`                          | `latest` (Python installer)     |
| python               | `n/a`                          | `3.10.x` (Ubuntu 22.04 default) |
| sops                 | `sops_version`                 | `3.7.3`                         |
| terraform            | `terraform_version`            | `1.3.5`                         |
| terraform-docs       | `terraform_docs_version`       | `0.16.0`                        |
| terragrunt           | `terragrunt_version`           | `0.40.2`                        |
| unzip                | `n/a`                          | `latest`                        |
| vals                 | `vals_version`                 | `0.19.0`                        |
| vault                | `vault_version`                | `1.12.1`                        |
| yq                   | `yq_version`                   | `4.30.4`                        |


## Install and provision

### Requirements

- [WSL2 for Windows](https://docs.microsoft.com/en-us/windows/wsl/install) (version `0.67.6` or higher for [systemd](https://devblogs.microsoft.com/commandline/systemd-support-is-now-available-in-wsl) to work)
- Ubuntu 22.04 (available in Microsoft Store)
- (recommended) [Windows Terminal](https://docs.microsoft.com/en-us/windows/terminal/install)


### WSL setup

```powershell
# Run powershell as administrator
wsl --install

# Check wsl version and update if needed
wsl --version
wsl --update
```

Install and launch Ubuntu 22.04, then edit `/etc/wsl.conf` with your favorite editor
```toml
[boot]
systemd=true
```

Restart Ubuntu 22.04
```powershell
wsl --shutdown
```

Launch again Ubuntu 22.04 and check that systemd is working
```bash
systemctl list-unit-files --type=service
```


### Provision

```bash
# Clone this repository inside WSL
git clone git@github.com:undebutant/wsl-devkit.git

# Go to the cloned folder and customize ansible variables
cd ./wsl-devkit/ansible
nano ./vars.yml
```

You need to edit Ansible variables before running the provision code, for example
```yaml
---
ubuntu_user: 'undeb'
user_timezone: 'Europe/Paris'

go_projects_path: '/home/undeb/lab/go'
```

After that, you are all set to provision your devkit
```bash
# Install the latest pip and ansible versions
cd ../scripts
./pip_ansible_install.sh

# Provision everything else with ansible (add --diff for details on the changes)
cd ../ansible
ansible-playbook -i inventory.yml playbook.yml --diff

# To provision specific roles with custom variables
ansible-playbook -i inventory.yml -t go -e "go_version=1.17.5" playbook.yml --diff
ansible-playbook -i inventory.yml -t terraform -e "terraform_version=1.0.11 terragrunt_version=0.34.3" playbook.yml --diff
```


### Additional configuration

Do not forget to source the bash scripts created by some roles
```bash
# Add the following lines in your .bashrc or .zshrc
if [ -f /etc/profile.d/ansible.sh ]; then
    source /etc/profile.d/ansible.sh
fi

if [ -f /etc/profile.d/go.sh ]; then
    source /etc/profile.d/go.sh
fi
```
