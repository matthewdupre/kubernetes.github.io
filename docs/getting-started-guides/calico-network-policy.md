---
---

This document describes how to deploy Kubernetes on GCE with Calico network policy using kube-up, and demonstrates basic network policy.

For more information on Project Calico, visit [projectcalico.org](http://projectcalico.org) and the [calico-containers repository](https://github.com/projectcalico/calico-containers).

This guide will set up a simple Kubernetes cluster with a single Kubernetes master and three Kubernetes nodes.

## Prerequisites

1. You need a Google Cloud Platform account with billing enabled. Visit the [Google Developers Console](http://cloud.google.com/console) for more details.
1. Install `gcloud` as necessary. `gcloud` can be installed as a part of the [Google Cloud SDK](https://cloud.google.com/sdk/).
1. Enable the [Compute Engine Instance Group Manager API](https://developers.google.com/console/help/new/#activatingapis) in the [Google Cloud developers console](https://console.developers.google.com).
1. Make sure that gcloud is set to use the Google Cloud Platform project you want. You can check the current project using `gcloud config list project` and change it via `gcloud config set project <project-id>`.
1. Make sure you have credentials for GCloud by running ` gcloud auth login`.
1. Make sure you can start up a GCE VM from the command line.  At least make sure you can do the [Create an instance](https://cloud.google.com/compute/docs/instances/#startinstancegcloud) part of the GCE Quickstart.
1. Make sure you can ssh into the VM without interactive prompts.  See the [Log in to the instance](https://cloud.google.com/compute/docs/instances/#sshing) part of the GCE Quickstart.

## Create the cluster

1. Obtain a copy of Kubernetes

```shell
git clone https://github.com/kubernetes/kubernetes.git
```

1. Create a cluster on GCE with Calico network policy.

```shell
cd kubernetes
NETWORK_POLICY_PROVIDER=calico cluster/kube-up.sh
```

Note: currently this

## Set up the master

















### NAT on the nodes

The simplest method for enabling connectivity from containers to the internet is to use outgoing NAT on your Kubernetes nodes.

Calico can provide outgoing NAT for containers.  To enable it, use the following `calicoctl` command:

```shell
ETCD_AUTHORITY=<master_ip:6666> calicoctl pool add <CONTAINER_SUBNET> --nat-outgoing
```

By default, `<CONTAINER_SUBNET>` will be `192.168.0.0/16`.  You can find out which pools have been configured with the following command:

```shell
ETCD_AUTHORITY=<master_ip:6666> calicoctl pool show
```

### NAT at the border router

In a data center environment, it is recommended to configure Calico to peer with the border routers over BGP. This means that the container IPs will be routable anywhere in the data center, and so NAT is not needed on the nodes (though it may be enabled at the data center edge to allow outbound-only internet connectivity).

The Calico documentation contains more information on how to configure Calico to [peer with existing infrastructure](https://github.com/projectcalico/calico-containers/blob/master/docs/ExternalConnectivity.md).

## Support Level


IaaS Provider        | Config. Mgmt | OS     | Networking  | Docs                                              | Conforms | Support Level
-------------------- | ------------ | ------ | ----------  | ---------------------------------------------     | ---------| ----------------------------
Bare-metal           | custom       | Ubuntu | Calico      | [docs](/docs/getting-started-guides/ubuntu-calico)                          |          | Community ([@djosborne](https://github.com/djosborne))

For support level information on all solutions, see the [Table of solutions](/docs/getting-started-guides/#table-of-solutions) chart.

