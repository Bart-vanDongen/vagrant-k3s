Vagrant.configure("2") do |config|
  config.vm.box = "generic/rocky9" # Rocky Linux 9 compatible with libvirt
  
  config.vm.provider "libvirt" do |v|
    v.memory = 2048
    v.cpus = 2
  end

  settings = YAML.load_file('settings.yml')

  # Setup VM and install k3s, Helm, and configure Kubernetes cluster
  config.vm.provision "shell", inline: <<-SHELL
    # Update system and install prerequisites
    dnf -y update
    dnf -y install curl git vim

    # Install kubectl
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
    chmod +x kubectl
    sudo mv ./kubectl /bin/kubectl

    # Install k3s (Lightweight Kubernetes)
    curl -sfL https://get.k3s.io | sh -

    # Install Helm
    curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
    sudo mv /usr/local/bin/helm /bin/helm

    # Set KUBECONFIG in home directory
    cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
    cp /etc/rancher/k3s/k3s.yaml /home/vagrant/.kube/config
    chown vagrant:vagrant /home/vagrant/.kube/config

    export KUBECONFIG=/etc/rancher/k3s/k3s.yaml

    # Wait for k3s to be ready
    until kubectl get nodes; do sleep 5; done
  SHELL

  # Deploy items using Helm from settings file
  settings['helm_installs'].each do |release_name, chart|
    helm_install_cmd = "helm repo add #{chart['chart']} #{chart['repo']} && helm repo update && helm upgrade --install #{chart['name']} #{release_name}/#{chart['chart']}"
    if chart.key?('values')
      helm_install_cmd += " --set #{chart['values'].map { |k, v| "#{k}=#{v}" }.join(',')}"
    end
    if chart.key?('values_file')
      helm_install_cmd += " -f #{chart['values_file']}"
    end
    config.vm.provision "shell", inline: helm_install_cmd
  end

  # Network Configuration
  config.vm.network "private_network", type: "dhcp", auto_config: true
end
