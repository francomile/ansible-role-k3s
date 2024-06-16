# K3s role

## Actions of the Role

* Install K3s server and agents

## Common Usage

```yaml
roles:
  - {
    role: k3s,
    tags: ["k3s"]
    }
```

Example inventory

```yaml
---
all:
  children:
    servers:
      hosts:
        # First server will be the one used for k3s_url var, and
        # will be the one agents are goping to connect.
        k3s-server01:
          ansible_host: "100.64.64.64"
          k3s_node_label_role: "server"
    agents:
      hosts:
        k3s-agent01:
          ansible_host: "100.64.64.65"
          k3s_node_label_role: "agent"
        k3s-agent02:
          ansible_host: "100.64.64.66"
          k3s_node_label_role: "agent"
  vars:
    # k3s important vars
    k3s_version: "v1.28.4+k3s2"
    k3s_token: "mysecret"
    k3s_api_port: 6443
    k3s_url: "https://{{ hostvars[groups['servers'][0]]['ansible_host'] }}:{{ k3s_api_port }}"
    k3s_server_install_args: >-
      --node-ip="{{ ansible_default_ipv4.address }}"
      --node-external-ip="{{ ansible_default_ipv4.address }}"
      --cluster-cidr=198.18.0.0/16
      --service-cidr=198.19.0.0/16
      --cluster-dns=198.19.0.10
      --node-label server-host="{{ k3s_node_label_host }}"
      --node-label server-role="{{ k3s_node_label_role }}"
    k3s_agent_install_args: >-
      --node-ip="{{ ansible_default_ipv4.address }}"
      --node-external-ip="{{ ansible_default_ipv4.address }}"
      --node-label server-host="{{ k3s_node_label_host }}"
      --node-label server-role="{{ k3s_node_label_role }}"
    k3s_install_cert_cronjob: false
    # General vars
    ansible_ssh_private_key_file: "~/.ssh/id_rsa"
    ansible_ssh_common_args: "-o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no"
    ansible_python_interpreter: "/usr/bin/python3"
    ansible_user: "root"
```

## Run the playbook

```shell
# Install k3s
ansible-playbook -i inventory playbook.yaml --tags "k3s"
# Remove k3s
ansible-playbook -i inventory playbook.yaml -e '{"k3s_remove_k3s": true}'
```

## Dependencies

* Docker
