---
layout: default
title: Basics
nav_order: 2
has_children: true
permalink: /basics
---

# Basics
{: .no_toc }
{: .fs-9 }

Let's start with the basics that we need to construct this thing.

This environment will be:
- Automated (as much as possible)
- Highly-Available
- Secure

---
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

----

## Network
So how do we fulfill the requirements above in regards to networking?

### Automated
- Kubernetes will manage the network environment using the `Weave` overlay network system to provide network connectivity
between all the different components in the cluster, this is completely automated from a management perspective, no ip 
addresses to manage.

### Highly-Available
- Whenever possible the services are run in highly-available configurations and are load-balanced using Traefik.

### Secure
- Traefik is configured with a [Minimal forward authentication service](https://github.com/thomseddon/traefik-forward-auth)
that requires the user to be logged in to an authorized Google account before allowing access to any service. This is done
without having an in-line proxy server (unlike oauth2_proxy).
- Let's Encrypt certificates are used for all traffic.
- Request are redirected from http to https automatically.

## Storage
The storage is divided in to two pieces, the configuration data and the data warehouse.
- Configuration data is shared in the cluster using a distributed Ceph cluster.
- Data warehousing is done using a NAS.

### Automated
Ceph is managed by Rook and this removes the need for manually deploying OSDs, MONs, etc. when adding nodes to the cluster.
Kubernetes PersistantVolumeClaims are used to provision the configuration storage on top of the Ceph cluster.

### Highly-Available
Ceph provides a replicated storage cluster that insures that no data-loss happens.
The NAS provides redundant deep storage of the actual content that is being managed.

### Secure

## Compute
