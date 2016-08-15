---
---

This document describes how to deploy Kubernetes on GCE with Calico network policy using kube-up, and demonstrates basic network policy.

For more information on Project Calico, visit [projectcalico.org](http://projectcalico.org) and the [calico-containers repository](https://github.com/projectcalico/calico-containers).

## Prerequisites

1. You need a Google Cloud Platform account with billing enabled. Visit the [Google Developers Console](http://cloud.google.com/console) for more details.
1. Install `gcloud` as necessary. `gcloud` can be installed as a part of the [Google Cloud SDK](https://cloud.google.com/sdk/).
1. Enable the [Compute Engine Instance Group Manager API](https://developers.google.com/console/help/new/#activatingapis) in the [Google Cloud developers console](https://console.developers.google.com).
1. Make sure that gcloud is set to use the Google Cloud Platform project you want. You can check the current project using `gcloud config list project` and change it via `gcloud config set project <project-id>`.
1. Make sure you have credentials for GCloud by running ` gcloud auth login`.
1. Make sure you can start up a GCE VM from the command line.  At least make sure you can do the [Create an instance](https://cloud.google.com/compute/docs/instances/#startinstancegcloud) part of the GCE Quickstart.
1. Make sure you can ssh into the VM without interactive prompts.  See the [Log in to the instance](https://cloud.google.com/compute/docs/instances/#sshing) part of the GCE Quickstart.

## Create the cluster

1. Obtain a copy of Kubernetes.  The example below will use the latest master revision, but you can also download the latest release (at least 1.4) from [this page](https://github.com/kubernetes/kubernetes/releases).  Note that at the time of writing there's no release containing this function.

    ```shell
    git clone https://github.com/kubernetes/kubernetes.git
    cd kubernetes
    ```

2. If you want more than one cluster running in your project, want to use a different name, or want a different number of worker nodes, see the `<kubernetes>/cluster/gce/config-default.sh` file for more fine-grained configuration before you start up your cluster.

3. Create a cluster on GCE with Calico network policy.  Add any additional configuration from step 2 in here.

    ```shell
    NETWORK_POLICY_PROVIDER=calico cluster/kube-up.sh
    ```

If you run into trouble, please see the section on [troubleshooting](/docs/getting-started-guides/gce/#troubleshooting) in the main GCE guide, or come ask questions on the [Kubernetes](/docs/troubleshooting/#slack) or [Calico](https://slack.projectcalico.org) Slack channels.

## Using the cluster

For general usage of the cluster other than network policy, consult the main [GCE getting started guide](/docs/getting-started-guides/gce).  This section details how to use the additional network policy function added by Calico.

This guide uses the kubectl script included in the repository to remotely manage the cluster, aliased to the standard `kubectl` name.  You can also run through these steps directly on the cluster itself.

```shell
alias kubectl=cluster/kubectl.sh
```

1. Check Calico pods are running: there should be an etcd pod, a policy controller pod, and one calico-node per minion.  It's normal to see a couple of restarts on the node and policy controller while calico-etcd is starting.

    ```shell
    $ kubectl get pods --namespace=kube-system
    NAME                                                 READY     STATUS    RESTARTS   AGE
    calico-etcd-0                                        1/1       Running   0          2h
    calico-node-kubernetes-minion-group-jck6             1/1       Running   0          2h
    calico-node-kubernetes-minion-group-k9jy             1/1       Running   1          2h
    calico-node-kubernetes-minion-group-szgr             1/1       Running   0          2h
    calico-policy-controller-65rw1                       1/1       Running   1          2h
    ...
    ````

2. Deploy the guestbook example and wait for each pod to reach 'Running' state.  Ctrl-C the watch once the pods are all running.

    ```shell
    $ kubectl create -f examples/guestbook/all-in-one/guestbook-all-in-one.yaml
    $ kubectl get pods --watch  # Ctrl-C once all pods are running.
    ```



## Tearing down the cluster

To remove/delete/teardown the cluster, use the `kube-down.sh` script.

```shell
cluster/kube-down.sh
```

## Support Level

IaaS Provider        | Config. Mgmt | OS     | Networking  | Docs                                              | Conforms | Support Level
-------------------- | ------------ | ------ | ----------  | ---------------------------------------------     | ---------| ----------------------------
GCE           | shell / Saltstack       | GCI / Debian | GCE / Calico      | [docs](/docs/getting-started-guides/calico-network-policy)                          |          | Community ([@matthewdupre](https://github.com/matthewdupre), [@caseydavenport](https://github.com/caseydavenport))

For support level information on all solutions, see the [Table of solutions](/docs/getting-started-guides/#table-of-solutions) chart.
