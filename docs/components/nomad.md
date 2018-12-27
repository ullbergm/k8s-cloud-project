---
layout: default
title: Nomad
nav_order: 3
parent: Components
---

# Install Nomad
{: .no_toc }
{: .fs-9 }

---

## Download Nomad

```bash
NOMAD_VERSION="0.8.6"
curl --silent --remote-name https://releases.hashicorp.com/nomad/${NOMAD_VERSION}/nomad_${NOMAD_VERSION}_linux_amd64.zip
```

## Install Nomad
```bash
unzip nomad_${NOMAD_VERSION}_linux_amd64.zip
rm -f nomad_${NOMAD_VERSION}_linux_amd64.zip
sudo chown root:root nomad
sudo mv nomad /usr/local/bin/
nomad --version
nomad -autocomplete-install
complete -C /usr/local/bin/nomad nomad
```

## Create Nomad user
```bash
sudo useradd --system --home /etc/nomad.d --shell /bin/false nomad
sudo mkdir --parents /opt/nomad
sudo chown --recursive nomad:nomad /opt/nomad
```

## Install Nomad service
```bash
cat <<EOF | sudo tee /etc/systemd/system/nomad.service
[Unit]
Description="HashiCorp Nomad - An application and service scheduler"
Documentation=https://www.nomad.io/docs/
Requires=network-online.target
After=network-online.target
ConditionFileNotEmpty=/etc/nomad.d/nomad.hcl

[Service]
User=nomad
Group=nomad
ExecStart=/usr/local/bin/nomad agent -config=/etc/nomad.d/
ExecReload=/bin/kill --signal HUP $MAINPID
KillMode=process
Restart=on-failure
RestartSec=2
StartLimitBurst=3
StartLimitIntervalSec=10
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
EOF
```

#### Configure Nomad
```bash
sudo mkdir --parents /etc/nomad.d
sudo touch /etc/nomad.d/nomad.hcl
sudo chown --recursive nomad:nomad /etc/nomad.d
sudo chmod 640 /etc/nomad.d/nomad.hcl
cat <<EOF | sudo tee -a /etc/nomad.d/nomad.hcl
datacenter = "home"
data_dir = "/opt/nomad"
EOF

sudo mkdir --parents /etc/nomad.d
sudo touch /etc/nomad.d/server.hcl
sudo chown --recursive nomad:nomad /etc/nomad.d
sudo chmod 640 /etc/nomad.d/server.hcl
cat <<EOF | sudo tee -a /etc/nomad.d/server.hcl
server {
  enabled = true
  bootstrap_expect = 3
}
EOF

sudo mkdir --parents /etc/nomad.d
sudo touch /etc/nomad.d/client.hcl
sudo chown --recursive nomad:nomad /etc/nomad.d
sudo chmod 640 /etc/nomad.d/client.hcl
cat <<EOF | sudo tee /etc/nomad.d/client.hcl
client {
  enabled = true
  servers = ["127.0.0.1:4647"]
  options = {
    "driver.raw_exec.enable" = "1"
  }
}
EOF
sudo systemctl enable nomad
sudo systemctl start nomad
sudo systemctl status nomad
```

## Create Nomad cluster
Only on the first host
{: .label }
```bash
sudo nomad server join 192.168.70.11 192.168.70.12 192.168.70.13
```
