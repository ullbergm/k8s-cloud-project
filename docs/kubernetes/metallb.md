---
layout: default
title: MetalLB
nav_order: 2
parent: Kubernetes Cluster
---

# MetalLB
{: .no_toc }
{: .fs-9 }

---

## Install MetalLB LoadBalancer
Only on the first node
{: .label }
```bash
kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.7.3/manifests/metallb.yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: config
  namespace: metallb-system
data:
  config: |
    peers:
    - peer-address: 192.168.69.1
      peer-asn: 65530
      my-asn: 65531
    address-pools:
    - name: default
      protocol: bgp
      addresses:
      - 192.168.70.240/28
    - name: special
      protocol: bgp
      addresses:
      - 192.168.70.10/32
      - 192.168.70.239/32
      auto-assign: false
EOF
```
