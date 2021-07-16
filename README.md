# Ansible Role: PKI

[![ubuntu-18](https://img.shields.io/badge/ubuntu-18.x-orange?style=flat&logo=ubuntu)](https://ubuntu.com/)
[![ubuntu-20](https://img.shields.io/badge/ubuntu-20.x-orange?style=flat&logo=ubuntu)](https://ubuntu.com/)
[![CentOS-8](https://img.shields.io/badge/centos-8.x-orange?style=flat&logo=centos)](https://www.centos.org/)

[![License](https://img.shields.io/badge/license-MIT%20License-brightgreen.svg?style=flat)](https://opensource.org/licenses/MIT)
[![GitHub issues](https://img.shields.io/github/issues/OnkelDom/ansible-role-pki?style=flat)](https://github.com/OnkelDom/ansible-role-pki/issues)
[![GitHub tag](https://img.shields.io/github/tag/OnkelDom/ansible-role-pki.svg?style=flat)](https://github.com/OnkelDom/ansible-role-pki/tags)
[![GitHub action](https://github.com/OnkelDom/ansible-role-pki/workflows/ansible-lint/badge.svg)](https://github.com/OnkelDom/ansible-role-pki)

## Description

Ansible Role to manage a Private Key Infrastructure for CA and Server Certs

## Requirements

- Ansible >= 2.5 (It might work on previous versions, but we cannot guarantee it)

## Role Variables

All variables which can be overridden are stored in [defaults/main.yml](defaults/main.yml) file as well as in table below.

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `pki_authorities` | [] | Setting this variable will disable searching for other vars containing authorities |
| `pki_search_authorities_pattern` | "pki_authorities_" | Variable name pattern to search ansible vars for other authority definitions |
| `pki_authorities_roots` | [defaults/main.yml](defaults/main.yml#10) | Example variables defining a certificate authorities |
| `pki_authorities_intermediates` | [defaults/main.yml](defaults/main.yml#26) | Example variables defining a certificate intermediates |
| `pki_install_ca` | [] | example variable of CA to install |
| `pki_regen_ca` | "" | set this to the name of a CA to regenerate, or to 'true' to regenerate all |
| `pki_trust_store_location` | [defaults/main.yml](defaults/main.yml#57) | locations of system trust stores to install CA certs to |
| `pki_certificates` | [] | Setting this variable will disable searching for other vars containing certificates |
| `pki_search_certificates_pattern` | "pki_certificates_" | Variable name pattern to search ansible vars for other certificate definitions |
| `pki_certificates_default` | [defaults/main.yml](defaults/main.yml#69) | Example variable defining a server certificate |
| `pki_regen_cert` | "" | set this to the name of the certificate to regenerate, or to 'true' to regenerate all |
| `pki_setup_host` | localhost | host where the generated PKI files are kept |
| `pki_setup_host_python_interpreter` | "{{ (pki_setup_host == 'localhost') \| ternary(ansible_playbook_python, ansible_facts['python']['executable']) }}" | Python interpreter that will be used during cert generation |
| `pki_dir` |"/etc/pki"  | base directory for the CA and server certificates |
| `pki_ca_dirs` | "{{ _pki_ca_dirs }}" | subdirectories to be created for holding CA certs/keys/csr |
| `pki_cert_dirs` | "{{ _pki_cert_dirs }}" | subdirectories to be created for holding server certs/keys/csr |
| `pki_install_certificates` | [defaults/main.yml](defaults/main.yml#107) | certificates to install |
| `pki_method` | standalone | method used to create the certificates |

## Example
```yaml
# CA certificates to create
# Setting this variable will disable searching for other vars containing authorities
# pki_authorities: []

# Variable name pattern to search ansible vars for other authority definitions
pki_search_authorities_pattern: "pki_authorities_"

# Example variables defining a certificate authorities
# pki_authorities_roots:
#   - name: "SnakeRoot"
#     provider: selfsigned
#     email_address: "pki@snakeoil.com"
#     basic_constraints: "CA:TRUE"
#     cn: "Snake Oil Corp Root CA"
#     country_name: "GB"
#     state_or_province_name: "England"
#     organization_name: "Snake Oil Corporation"
#     organizational_unit_name: "IT Security"
#     key_usage:
#       - digitalSignature
#       - cRLSign
#       - keyCertSign
#     not_after: "+3650d"

#pki_authorities_intermediates:
#   - name: "SnakeRootIntermediate"
#     email_address: "pki@snakeoil.com"
#     provider: ownca
#     cn: "Snake Oil Corp Openstack Infrastructure Intermediate CA"
#     country_name: "GB"
#     state_or_province_name: "England"
#     organization_name: "Snake Oil Corporation"
#     organizational_unit_name: "IT Security"
#     key_usage:
#       - digitalSignature
#       - cRLSign
#       - keyCertSign
#     not_after: "+365d"
#     signed_by: "SnakeRoot"

# example variable of CA to install
# pki_install_ca:
#   # CA created but the PKI role
#   - name: SnakeRoot
#
#   # user provided CA copied from the deploy host (src), to the target (filename)
#   - src: /opt/my-ca/MyRoot.crt
#     filename: /etc/ssl/certs/MyRoot.crt
#
pki_install_ca: []

# set this to the name of a CA to regenerate, or to 'true' to regenerate all
pki_regen_ca: ''

# locations of system trust stores to install CA certs to
pki_trust_store_location:
  apt: /usr/local/share/ca-certificates/
  dnf: /etc/pki/ca-trust/source/anchors/

# Server certificates to create
# Setting this variable will disable searching for other vars containing certificates
# pki_certificates: []

# Variable name pattern to search ansible vars for other certificate definitions
pki_search_certificates_pattern: "pki_certificates_"

# Example variable defining a server certificate
# pki_certificates_default:
#   - name: "SnakeWeb"
#     provider: ownca
#     cn: "www.snakeoil.com"
#     san: "DNS:www.snakeoil.com,DNS:snakeoil.com"
#   - name: "SnakeMail"
#     signed_by: "SnakeRootIntermediate"
#     provider: ownca
#     cn: "imap.snakeoil.com"
#     signed_by: "SnakeRootIntermediate"

# Example variable defining a server certificate from ansible host variables
# pki_certificates_default:
#   - name: "myservice_{{ ansible_facts['hostname'] }}"
#     cn: "{{ ansible_facts['hostname'] }}"
#     provider: ownca
#     san: "{{ 'DNS:' ~ ansible_facts['hostname'] ~  ',DNS:' ~ ansible_facts['fqdn'] ~ ',IP:' ~ ansible_facts['default_ipv4'] }}"
#     signed_by: "SnakeRootIntermediate"

# set this to the name of the certificate to regenerate, or to 'true' to regenerate all
pki_regen_cert: ''

# host where the generated PKI files are kept
pki_setup_host: localhost

# Python interpreter that will be used during cert generation
pki_setup_host_python_interpreter: "{{ (pki_setup_host == 'localhost') | ternary(ansible_playbook_python, ansible_facts['python']['executable']) }}"

# base directory for the CA and server certificates
pki_dir: "/etc/pki"

# subdirectories to be created for holding CA certs/keys/csr
pki_ca_dirs: "{{ _pki_ca_dirs }}"

# subdirectories to be created for holding server certs/keys/csr
pki_cert_dirs: "{{ _pki_cert_dirs }}"

# certificates to install
pki_install_certificates: []

# Example variable for installation of server certificates with optional user supplied cert override
# pki_install_certificates:
#     # server certificate
#   - src: "{{ user_ssl_cert | default(pki_dir ~ '/certs/certs/myservice_' ~ ansible_facts['hostname'] ~ '.crt') }}"
#     dest: "{{ myservice_ssl_cert }}"
#     owner: "root"
#     group: "root"
#     mode: "0644"
#     #private key
#   - src: "{{ myservice_user_ssl_key | default(pki_dir ~ 'certs/keys/myservice_' ~ ansible_facts['hostname'] ~ '.key.pem') }}"
#     dest: "{{ myservice_ssl_key }}"
#     owner: "myservice"
#     group: "myservice"
#     mode: "0600"
#     # intermediate CA
#   - src: "{{ myservice_user_ssl_ca_cert | default(pki_dir ~ '/roots/SnakeRootIntermediate/certs/SnakeRootIntermediate.crt' }}"
#     dest: "{{ myservice_ssl_ca_cert }}"
#     owner: "myservice"
#     group: "myservice"
#     mode: "0644"

# method used to create the certificates
pki_method: standalone
```

### Playbook

```yaml
---
- hosts: all
  roles:
  - onkeldom.pki
```

## Contributing

See [contributor guideline](CONTRIBUTING.md).

## License

This project is licensed under MIT License. See [LICENSE](/LICENSE) for more details.
