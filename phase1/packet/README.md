# Getting Started on Packet

## Overview

This will deploy a Kubernetes cluster into Packet for Intel and ARM servers.

## Preparation

Required software:
  * `git` for retrieving a copy of this software
  * `docker` for executing the `kubernetes-anywhere` deployment; version XXX required
  * `make` for entering the deployment environment
  * `kubectl` for working with the cluster after deployment

Required information:
  * Packet account
  * Packet credentials (XXX)

## Deployment

#### Clone the `kubernetes-anywhere` tool:

```shell
git clone https://github.com/kubernetes/kubernetes-anywhere
cd kubernetes-anywhere
```

#### Enter the `kubernetes-anywhere` deployment environment:

```shell
make docker-dev
```

#### Start the deployment wizard:

```shell
make deploy
```

**Notes**:
* The name chosen for `phase1.cluster_name` needs to be globally unique. (See ["Cluster Naming Restrictions"](#cluster-naming-restrictions) below for full details)

* To properly boot a cluster on Packet, you MUST set these values in the wizard:

  ```
  * phase1.packet.XXX - "XXX"
  ```

  You may skip these fields, and the deployment will offer to fill them in for you:

  ```
  * phase1.packet.YYY
  * phase1.packet.ZZZ
  ```

* If you see this error output, please wait a minute and re-execute `make deploy`. This is a known issue with XXX.


## Congratulations!

You have a Kubernetes cluster!

Let's copy your `kubeconfig.json` file somewhere for safe-keeping.
*(Note, this will overwrite any existing config file at `~/.kube/config`.)*
You'll need to do this outside of the `kubernetes-anywhere` deployment environment so that it is usable later.

```shell
mkdir -p ~/.kube
cp ./phase1/packet/.tmp/kubeconfig.json ~/.kube/config
```


#### Deploy Something to the Cluster

  Your cluster is ready to use!

  If you want to prove to yourself that it works, you can try:

  * (Note, if you stored `kubeconfig.json` in a location other than `$HOME/.kube/config` you need to set `KUBECONFIG` appropriately):
  ```shell
  export KUBECONFIG=$HOME/.kube/config
  ```

  1. Start an `nginx` deployment:
  ```shell
  kubectl run nginx --image=nginx --replicas=3
  ```

  2. Expose the `nginx` deployment via a Service (Type=XXX):
  ```shell
  kubectl expose deployment nginx --name=nginx --type=XXX --port=80
  ```

  3. Wait a couple minutes for the Azure Load Balancer to be provisioned, then:
  ```shell
  kubectl get service nginx
  ```

  4. Once XXX has finished provisioning, you will see the external IP address where `nginx` is now
  accessible. If you see `<pending>` then you need to wait a few more minutes.

Enjoy your Kubernetes cluster on Packet!

## Troubleshooting

### Validation Fails (Zero nodes are healthy)
If no nodes are available, there was likely a provisioning failure on the master.
The following steps will help in troubleshooting:

1. SSH to the master.
2. Use the following commands to upload relevant logs:
  * `sudo journalctl -u kubelet 2>&1 | nc termbin.com 9999`
  * `sudo cat /var/log/cloud-init-output.log | nc termbin.com 9999`
3. Attach the logs to [a new Issue](https://github.com/kubernetes/kubernetes-anywhere/issues/new) in this repository.

### Validation Fails (One or more nodes are missing/unhealthy)

1. Use `kubectl get nodes` to identify the missing nodes.
2. Use XXX to find the node and the node's private IPv4 address.
3. SSH to the master, then to the missing node
4. Use the following commands to upload relevant logs:
  * `sudo journalctl -u kubelet 2>&1 | nc termbin.com 9999`
  * `sudo cat /var/log/cloud-init-output.log | nc termbin.com 9999`
5. Attach the logs to [a new Issue](https://github.com/kubernetes/kubernetes-anywhere/issues/new) in this repository.

## Notes

### Cluster Naming Restrictions

Are there any? There shouldn't be. XXXX
The `cluster_name` is used as a prefix for all created resources and is used as the storage account name (dashes are automatically removed). The following restrictions apply to storage account names, and thus must be respected when choosing a `cluster_name`:
  * Must contain at least 3 characters
  * Must contain no more than 24 characters
  * Must only use alphanumeric characters and underscores (same as domain name requirements)
  * Must start with a letter

### Between Deployments
At times, it can be helpful to run `make clean`. Note that this will remove the Terraform state files, requiring you to manually remove the cluster. Fortunately this is easy in Azure, and just requires you to remove the resource group created by the deployment.

### XXX Permission Scope

Something about API keys XXX

### XXX Service Principal Creation
The deployment process will offer to create a service principal for you if you choose to
omit the relevant fields. If you would like to create the service principal and specify the
credentials manually, you may do so. Details XXX.
