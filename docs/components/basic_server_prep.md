---
layout: default
title: Basic server prep
nav_order: 4
parent: Components
---

# Basic server prep
{: .no_toc }
{: .fs-9 }

---

## Network interface
```bash
sudo sed -i -e 's/^GRUB_CMDLINE_LINUX=.*$/GRUB_CMDLINE_LINUX="net.ifnames=0 biosdevname=0"/' /etc/default/grub
sudo sed -i -e 's/ens160/eth0/' -e 's/ens192/eth0/' /etc/network/interfaces
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## Sudoers
```bash
echo "ubuntu ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ubuntu
```

## Timezone
```bash
sudo ln -sf /usr/share/zoneinfo/EST5EDT /etc/localtime
```

## SSH Keys
```bash
mkdir -p ~/.ssh
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCgE+n+8O5xXvl3P3Ry3vi/mw++CGcGQGAQOalBEZn/qTIhlSLZF2XPlGYUfa9VGCmWMK5v2lQRF+PTh+YXqKcI0P9voYcrLE4ZN1IfI0Uy2Z6oImy+3VgxSnA//yyxu7UZFazHZFKIXtc+5ORmz8zhApxjAzp9cpFLqHEcsve/wITGq+uXQnslaSq3v/rS2mEMV2EPR1/7LE0+fAQqBNqa6r+f7fF7Hx938dIwVnIjFJCMIRxJI/otrPmTVUnaIt9DB7W4UTz/s6JN2cAWqITZm5bCNZAojMEPzyYfgHLqBqE+VosMbNToylVNUYmU5w9WXtPbI5ZqP7zkYcvRRkMx ullbergm@github/22741083 # ssh-import-id gh:ullbergm" >>~/.ssh/authorized_keys
sudo mkdir -p /root/.ssh
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCgE+n+8O5xXvl3P3Ry3vi/mw++CGcGQGAQOalBEZn/qTIhlSLZF2XPlGYUfa9VGCmWMK5v2lQRF+PTh+YXqKcI0P9voYcrLE4ZN1IfI0Uy2Z6oImy+3VgxSnA//yyxu7UZFazHZFKIXtc+5ORmz8zhApxjAzp9cpFLqHEcsve/wITGq+uXQnslaSq3v/rS2mEMV2EPR1/7LE0+fAQqBNqa6r+f7fF7Hx938dIwVnIjFJCMIRxJI/otrPmTVUnaIt9DB7W4UTz/s6JN2cAWqITZm5bCNZAojMEPzyYfgHLqBqE+VosMbNToylVNUYmU5w9WXtPbI5ZqP7zkYcvRRkMx ullbergm@github/22741083 # ssh-import-id gh:ullbergm" | sudo tee -a /root/.ssh/authorized_keys
```

## Docker
```bash
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu
```

## Kubernetes
```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo kubeadm config images pull
sudo sed -i -e "/swap/d" /etc/fstab
sudo swapoff -a
echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee /etc/sysctl.d/50-bridge-nf-call.conf
sudo sysctl net.bridge.bridge-nf-call-iptables=1
```

## Reboot
```bash
sudo reboot
```
