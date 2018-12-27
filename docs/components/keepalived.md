---
layout: default
title: Keepalived
nav_order: 5
parent: Components
---

# Keepalived
{: .no_toc }
{: .fs-9 }

---

## ip_vs module
```bash
echo "ip_vs" | sudo tee -a /etc/modules-load.d/modules.conf
sudo modprobe ip_vs
```

## Install keepalived
```bash
sudo apt-get install -y keepalived
cat <<EOF | sudo tee /etc/keepalived/keepalived.conf
vrrp_script kubernetes {
   script "/usr/bin/curl -ksf https://localhost:6443/healthz"
   interval 10
   timeout 1
}
vrrp_instance VI_1 {
   state BACKUP
   nopreempt
   interface eth0
   virtual_router_id 51
   priority 150
   advert_int 1
   authentication {
     auth_type PASS
     auth_pass k8secret
   }
   virtual_ipaddress {
     192.168.70.9
   }
   track_script {
     kubernetes
   }
}
EOF
sudo systemctl restart keepalived
```
