$install_packages = <<SCRIPT
# Install Docker
dnf config-manager \
    --add-repo \
    https://download.docker.com/linux/fedora/docker-ce.repo
dnf update -y
dnf install -y docker-ce docker-ce-cli containerd.io
systemctl enable docker
systemctl start docker
usermod -a -G docker vagrant

# Install Go
dnf -y install golang-bin

# Install Kubectl
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
dnf install -y kubectl

# Install kind
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
chmod +x ./kind
mv ./kind /usr/bin/kind



SCRIPT

Vagrant.configure("2") do |config|
    config.vm.box = "fedora/34-cloud-base"
    config.vm.box_version = "34.20210423.0"

    config.vm.provider "virtualbox" do |v|
        v.name = "Fedora KIND"
        v.cpus = 4
        v.memory = 8192
    end

    # initialization scripts
    config.vm.provision "shell", inline: $install_packages

    # sharing info with host
    config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
    config.vm.network "forwarded_port", guest: 5601, host: 5601

end

