# Vagrant K3s Setup

This project sets up a Vagrant environment with K3s and Helm. It uses a Rocky Linux 9 base box and provisions the VM using a shell script.

## Prerequisites

- Vagrant
- Libvirt (or another provider supported by Vagrant)

## Setup

1. Clone this repository:
    ```sh
    git clone https://github.com/Bart-vanDongen/vagrant-k3s.git
    cd vagrant-k3s
    ```

2. Ensure you have the required Vagrant plugins installed:
    ```sh
    vagrant plugin install vagrant-libvirt
    ```

3. Customize the `settings.yml` file if needed. This file contains Helm chart configurations.


4. Start the Vagrant environment:
    ```sh
    vagrant up
    ```

5. Access the VM:
    ```sh
    vagrant ssh
    ```

6. Verify that K3s is running:
    ```sh
    vagrant ssh
    systemctl status k3s
    kubectl get nodes
    ```

## Configuration

- The `Vagrantfile` configures the VM with 2 CPUs and 2048 MB of memory.
- The `settings.yml` file contains Helm chart configurations that are deployed during provisioning.


## Using `settings.yml`

The `settings.yml` file is used to define Helm chart configurations that will be deployed during the provisioning process. Here is an example of how to configure it:

```yml
helm_installs:
  ingress-nginx:
    repo: https://kubernetes.github.io/ingress-nginx
    name: nginx
    chart: ingress-nginx
    # values:
    #   controller.replicaCount: 2
    #   controller.nodeSelector."kubernetes.io/os": linux
    # values_file: /path/to/your/values.yaml
```

- `repo`: The Helm chart repository URL.
- `name`: The release name for the Helm chart.
- `chart`: The name of the Helm chart.
- `values`: (Optional) A dictionary of values to set for the Helm chart.
- `values_file`: (Optional) A path to a values file to use for the Helm chart.


## Cleaning Up

To destroy the Vagrant environment, run:
```sh
vagrant destroy