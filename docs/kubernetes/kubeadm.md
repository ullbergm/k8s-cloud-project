---
layout: default
title: Kubeadm config
nav_order: 1
parent: kubernetes
---

# Kubeadm config
{: .no_toc }
{: .fs-9 }

---

## Kubeadm init
Only on the first node
{: .label }
```bash
sudo mkdir -p /config
cat <<EOF | sudo tee /config/kubeadm-config.yaml
apiVersion: kubeadm.k8s.io/v1beta1
kind: ClusterConfiguration
kubernetesVersion: stable
apiServer:
  certSANs:
  - "k8s.ullberg.family"
controlPlaneEndpoint: "k8s.ullberg.family:6443"
EOF
sudo kubeadm init --config=/config/kubeadm-config.yaml
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Weave Net
Only on the first node
{: .label }
```bash
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

## Wait for it to create the cluster

## Copy certs
Only on the first node
{: .label }
```bash
USER=ubuntu
HOSTS="192.168.70.12 192.168.70.13"
for host in ${HOSTS}; do
 echo $host
 sudo scp /etc/kubernetes/pki/ca.crt "${USER}"@$host:
 sudo scp /etc/kubernetes/pki/ca.key "${USER}"@$host:
 sudo scp /etc/kubernetes/pki/sa.key "${USER}"@$host:
 sudo scp /etc/kubernetes/pki/sa.pub "${USER}"@$host:
 sudo scp /etc/kubernetes/pki/front-proxy-ca.crt "${USER}"@$host:
 sudo scp /etc/kubernetes/pki/front-proxy-ca.key "${USER}"@$host:
 sudo scp /etc/kubernetes/pki/etcd/ca.crt "${USER}"@$host:etcd-ca.crt
 sudo scp /etc/kubernetes/pki/etcd/ca.key "${USER}"@$host:etcd-ca.key
 sudo scp /etc/kubernetes/admin.conf "${USER}"@$host:
done
```

## Move certs
On each of the other nodes
{: .label }
```bash
USER=ubuntu
sudo mkdir -p /etc/kubernetes/pki/etcd
sudo mv /home/${USER}/ca.crt /etc/kubernetes/pki/
sudo mv /home/${USER}/ca.key /etc/kubernetes/pki/
sudo mv /home/${USER}/sa.pub /etc/kubernetes/pki/
sudo mv /home/${USER}/sa.key /etc/kubernetes/pki/
sudo mv /home/${USER}/front-proxy-ca.crt /etc/kubernetes/pki/
sudo mv /home/${USER}/front-proxy-ca.key /etc/kubernetes/pki/
sudo mv /home/${USER}/etcd-ca.crt /etc/kubernetes/pki/etcd/ca.crt
sudo mv /home/${USER}/etcd-ca.key /etc/kubernetes/pki/etcd/ca.key
sudo mv /home/${USER}/admin.conf /etc/kubernetes/admin.conf
sudo kubeadm join k8s.ullberg.family:6443 --token ph3pe5.d6vvs77sh3ki7qkw --discovery-token-ca-cert-hash sha256:76917b0b1030409ddb272fcfb086bf484e17253d5e15502c7931aa332c708008 --experimental-control-plane
```

## Untaint masters
Only on the first node
{: .label }
```bash
kubectl taint nodes --all node-role.kubernetes.io/master-
```

## Install dashboard
Only on the first node
{: .label }
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
cat <<EOF | kubectl apply -f -
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: kubernetes-dashboard-external-https
  namespace: kube-system
spec:
  rules:
  - host: kube-dashboard.k8s.ullberg.family
    http:
      paths:
      - path: /
        backend:
          serviceName: kubernetes-dashboard
          servicePort: 443
EOF
```
