---
title: Cluster Options
weight: 2250
--- 

As you configure a new cluster that's provisioned using [RKE]({{< baseurl >}}/rke/latest/en/), you can choose custom Kubernetes options.

You can configure Kubernetes options one of two ways:

- [Rancher UI](#rancher-ui): Use the Rancher UI to select options that are commonly customized when setting up a Kubernetes cluster.
- [Config File](#config-file): The cluster config file allows you to use any option offered by Kubernetes by specifying them in YAML. In Rancher v2.0.0-v2.2.x, the config file is identical to the  [cluster config file for the Rancher Kubernetes Engine]({{<baseurl>}}/rke/latest/en/config-options/), which is the tool Rancher uses to provision clusters. In Rancher v2.3.0, the RKE information is still included in the config file, but it is separated from other options, so that the RKE cluster config options are nested under the `rancher_kubernetes_engine_config` directive.

## Rancher UI

When creating a cluster using one of the options described in [Rancher Launched Kubernetes]({{< baseurl >}}/rancher/v2.x/en/cluster-provisioning/rke-clusters), you can configure basic Kubernetes options using the **Cluster Options** section.

From this section you can choose:

- The version of Kubernetes installed on your cluster nodes. Rancher packages its own version of Kubernetes based on [hyperkube](https://github.com/rancher/hyperkube).

- The [Network Provider](https://kubernetes.io/docs/concepts/cluster-administration/networking/) that the cluster uses. For more details on the different networking providers, please view our [Networking FAQ]({{< baseurl >}}/rancher/v2.x/en/faq/networking/cni-providers/).

    >**Note:** After you launch the cluster, you cannot change your network provider. Therefore, choose which network provider you want to use carefully, as Kubernetes doesn't allow switching between network providers. Once a cluster is created with a network provider, changing network providers would require you  tear down the entire cluster and all its applications.

    Out of the box, Rancher is compatible with the following network providers:

    - <a id="canal"></a>[Canal](https://github.com/projectcalico/canal)

        In v2.0.0 - v2.0.4 and v2.0.6, this was the default option for these clusters was Canal with network isolation. With the network isolation automatically enabled, it prevented any pod communication between [projects]({{< baseurl >}}/rancher/v2.x/en/k8s-in-rancher/projects-and-namespaces/).

        As of v2.0.7, if you use Canal, you also have the option of using **Project Network Isolation**, which will enable or disable communication between pods in different [projects]({{< baseurl >}}/rancher/v2.x/en/k8s-in-rancher/projects-and-namespaces/).

        >**Attention Rancher v2.0.0 - v2.0.6 Users**
        >
        >- In previous Rancher releases, Canal isolates project network communications with no option to disable it. If you are using any of these Rancher releases, be aware that using Canal prevents all communication between pods in different projects.
        >- If you have clusters using Canal and are upgrading to v2.0.7, those clusters enable Project Network Isolation by default. If you want to disable Project Network Isolation, edit the cluster and disable the option.


    - [Flannel](https://github.com/coreos/flannel#flannel)

         In v2.0.5, this was the default option, which did not prevent any network isolation between projects.

    - [Calico](https://docs.projectcalico.org/)
    - [Weave](https://github.com/weaveworks/weave) (_Available as of v2.2.0_)

         When Weave is selected as network provider, Rancher will automatically enable encryption by generating a random password. If you want to specify the password manually, please see how to configure your cluster using a [Config File]({{< baseurl >}}/rancher/v2.x/en/cluster-provisioning/rke-clusters/options/#config-file) and the [Weave Network Plug-in Options]({{< baseurl >}}/rke/latest/en/config-options/add-ons/network-plugins/#weave-network-plug-in-options).


<br/>

- If you want to configure a [Kubernetes cloud provider]({{< baseurl >}}/rancher/v2.x/en/cluster-provisioning/rke-clusters/options/cloud-providers). If you want to use [volumes and storage]({{< baseurl >}}/rancher/v2.x/en/k8s-in-rancher/volumes-and-storage/) in Kubernetes, typically you must select the specific cloud provider in order to use it. For example, if you want to use Amazon EBS, you would need to select the `aws` cloud provider.

    >**Note:** If the cloud provider you want to use is not listed as an option, you will need to use the [config file option](#config-file) to configure the cloud provider. Please reference the [RKE cloud provider documentation]({{< baseurl >}}/rke/latest/en/config-options/cloud-providers/) on how to configure the cloud provider.

If you want to see all the configuration options for a cluster, please click **Show advanced options** on the bottom right. The advanced options are described below:

### Private registries

_Available as of v2.2.0_

The registry configuration here is applied during the provisioning of the cluster. This option tells Rancher where to pull the [system images]({{<baseurl>}}/rke/latest/en/config-options/system-images/) or [addon images.]({{<baseurl>}}/rke/latest/en/config-options/add-ons/)

- **System images** are components needed to maintain the Kubernetes cluster. 
- **Add-ons** are used to deploy several cluster components, including network plug-ins, the ingress controller, the DNS provider, or the metrics server.

To deploy workloads that pull images from a private registry, you will need to [set up your own Kubernetes registry]({{<baseurl>}}/rancher/v2.x/en/k8s-in-rancher/registries/) for your project.

See the [RKE documentation on private registries]({{< baseurl >}}/rke/latest/en/config-options/private-registries/) for more information on the private registry for components applied during the provisioning of the cluster.

### Authorized Cluster Endpoint

_Available as of v2.2.0_

Authorized Cluster Endpoint can be used to directly access the Kubernetes API server, without requiring communication through Rancher. This is enabled by default, using the IP of the node with the `controlplane` role and the default Kubernetes self signed certificates. It is recommended to create an FQDN pointing to a load balancer which load balances across your nodes with the `controlplane` role. If you are using private CA signed certificates on the load balancer, you have to supply the CA certificate which will be included in the generated kubeconfig to validate the certificate chain. See the [Kubeconfig Files]({{<baseurl>}}/rancher/v2.x/en/k8s-in-rancher/kubeconfig/) and [API Keys]({{<baseurl>}}/rancher/v2.x/en/user-settings/api-keys/#creating-an-api-key) documentation for more information.

### Advanced Cluster Options

#### Nginx Ingress

Option to enable or disable the [NGINX ingress controller]({{< baseurl >}}/rke/latest/en/config-options/add-ons/ingress-controllers/).

#### Node Port Range

Option to change the range of ports that can be used for [NodePort services](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport). Default is `30000-32767`.

#### Metrics Server Monitoring

Option to enable or disable [Metrics Server]({{< baseurl >}}/rke/latest/en/config-options/add-ons/metrics-server/).

#### Pod Security Policy Support

Option to enable and select a default [Pod Security Policy]({{< baseurl >}}/rancher/v2.x/en/admin-settings/pod-security-policies). You must have an existing Pod Security Policy configured before you can use this option.

#### Docker version on nodes

Option to require [a supported Docker version]({{< baseurl >}}/rancher/v2.x/en/installation/requirements/) installed on the cluster nodes that are added to the cluster, or to allow unsupported Docker versions installed on the cluster nodes.

#### Docker Root Directory

If the nodes you are adding to the cluster have Docker configured with a non-default Docker Root Directory (default is `/var/lib/docker`), please specify the correct Docker Root Directory in this option.

#### Recurring etcd Snapshots

Option to enable or disable [recurring etcd snaphots]({{< baseurl >}}/rke/latest/en/etcd-snapshots/#etcd-recurring-snapshots).

## Config File

>**Note:** In Rancher v2.0.5 and v2.0.6, the names of services in the Config File (YAML) should contain underscores only: `kube_api` and `kube_controller`.

Instead of using the Rancher UI to choose Kubernetes options for the cluster, advanced users can create an RKE config file. Using a config file allows you to set any of the [options available]({{< baseurl >}}/rke/latest/en/config-options/) in an RKE installation, except for `system_images` configuration. The `system_images` option is not supported when creating a cluster with the Rancher UI or API.

- To edit an RKE config file directly from the Rancher UI, click **Edit as YAML**.
- To read from an existing RKE file, click **Read from a file**.

![image]({{< baseurl >}}/img/rancher/cluster-options-yaml.png)

The structure of the config file is different depending on your version of Rancher. Below are example config files for Rancher v2.0.0-v2.2.x and for Rancher v2.3.0+.

### Config File Structure in Rancher v2.3.0+

RKE (Rancher Kubernetes Engine) is the tool that Rancher uses to provision Kubernetes clusters. Rancher's cluster config files used to have the same structure as [RKE config files,]({{<baseurl>}}/rke/latest/en/example-yamls/) but the structure changed so that in Rancher, RKE cluster config items are separated from non-RKE config items. Therefore, configuration for your cluster needs to be nested under the `rancher_kubernetes_engine_config` directive in the cluster config file. Cluster config files created with earlier versions of Rancher will need to be updated for this format. An example cluster config file is included below.

{{% accordion id="v2.3.0-cluster-config-file" label="Example Cluster Config File for Rancher v2.3.0+" %}}

```yaml
# 
# Cluster Config
# 
docker_root_dir: /var/lib/docker
enable_cluster_alerting: false
enable_cluster_monitoring: false
enable_network_policy: false
local_cluster_auth_endpoint:
  enabled: true
# 
# Rancher Config
# 
rancher_kubernetes_engine_config: # Your RKE template config goes here.
  addon_job_timeout: 30
  authentication:
    strategy: x509
  ignore_docker_version: true
# 
# # Currently only nginx ingress provider is supported.
# # To disable ingress controller, set `provider: none`
# # To enable ingress on specific nodes, use the node_selector, eg:
#    provider: nginx
#    node_selector:
#      app: ingress
# 
  ingress:
    provider: nginx
  kubernetes_version: v1.15.3-rancher3-1
  monitoring:
    provider: metrics-server
# 
#   If you are using calico on AWS
# 
#    network:
#      plugin: calico
#      calico_network_provider:
#        cloud_provider: aws
# 
# # To specify flannel interface
# 
#    network:
#      plugin: flannel
#      flannel_network_provider:
#      iface: eth1
# 
# # To specify flannel interface for canal plugin
# 
#    network:
#      plugin: canal
#      canal_network_provider:
#        iface: eth1
# 
  network:
    options:
      flannel_backend_type: vxlan
    plugin: canal
# 
#    services:
#      kube-api:
#        service_cluster_ip_range: 10.43.0.0/16
#      kube-controller:
#        cluster_cidr: 10.42.0.0/16
#        service_cluster_ip_range: 10.43.0.0/16
#      kubelet:
#        cluster_domain: cluster.local
#        cluster_dns_server: 10.43.0.10
# 
  services:
    etcd:
      backup_config:
        enabled: true
        interval_hours: 12
        retention: 6
        safe_timestamp: false
      creation: 12h
      extra_args:
        election-timeout: 5000
        heartbeat-interval: 500
      gid: 0
      retention: 72h
      snapshot: false
      uid: 0
    kube_api:
      always_pull_images: false
      pod_security_policy: false
      service_node_port_range: 30000-32767
  ssh_agent_auth: false
windows_prefered_cluster: false
```
{{% /accordion %}}

### Config File Structure in Rancher v2.0.0-v2.2.x

An example cluster config file is included below.

{{% accordion id="prior-to-v2.3.0-cluster-config-file" label="Example Cluster Config File for Rancher v2.0.0-v2.2.x" %}}
```yaml
addon_job_timeout: 30
authentication:
  strategy: x509
ignore_docker_version: true
# 
# # Currently only nginx ingress provider is supported.
# # To disable ingress controller, set `provider: none`
# # To enable ingress on specific nodes, use the node_selector, eg:
#    provider: nginx
#    node_selector:
#      app: ingress
# 
ingress:
  provider: nginx
kubernetes_version: v1.15.3-rancher3-1
monitoring:
  provider: metrics-server
# 
#   If you are using calico on AWS
# 
#    network:
#      plugin: calico
#      calico_network_provider:
#        cloud_provider: aws
# 
# # To specify flannel interface
# 
#    network:
#      plugin: flannel
#      flannel_network_provider:
#      iface: eth1
# 
# # To specify flannel interface for canal plugin
# 
#    network:
#      plugin: canal
#      canal_network_provider:
#        iface: eth1
# 
network:
  options:
    flannel_backend_type: vxlan
  plugin: canal
# 
#    services:
#      kube-api:
#        service_cluster_ip_range: 10.43.0.0/16
#      kube-controller:
#        cluster_cidr: 10.42.0.0/16
#        service_cluster_ip_range: 10.43.0.0/16
#      kubelet:
#        cluster_domain: cluster.local
#        cluster_dns_server: 10.43.0.10
# 
services:
  etcd:
    backup_config:
      enabled: true
      interval_hours: 12
      retention: 6
      safe_timestamp: false
    creation: 12h
    extra_args:
      election-timeout: 5000
      heartbeat-interval: 500
    gid: 0
    retention: 72h
    snapshot: false
    uid: 0
  kube_api:
    always_pull_images: false
    pod_security_policy: false
    service_node_port_range: 30000-32767
ssh_agent_auth: false
```
{{% /accordion %}}

### Default DNS provider

The table below indicates what DNS provider is deployed by default. See [RKE documentation on DNS provider]({{< baseurl >}}/rke/latest/en/config-options/add-ons/dns/) for more information how to configure a different DNS provider. CoreDNS can only be used on Kubernetes v1.12.0 and higher.

| Rancher version | Kubernetes version | Default DNS provider |
|-------------|--------------------|----------------------|
| v2.2.5 and higher | v1.14.0 and higher | CoreDNS |
| v2.2.5 and higher | v1.13.x and lower | kube-dns |
| v2.2.4 and lower | any | kube-dns |

### Rancher specific parameters

_Available as of v2.2.0_

Besides the RKE config file options, there are also Rancher specific settings that can be configured in the Config File (YAML):

#### docker_root_dir

See [Docker Root Directory](#docker-root-directory).

#### enable_cluster_monitoring

Option to enable or disable [Cluster Monitoring]({{< baseurl >}}/rancher/v2.x/en/cluster-admin/tools/monitoring/).

#### enable_network_policy

Option to enable or disable Project Network Isolation.

#### local_cluster_auth_endpoint

See [Authorized Cluster Endpoint](#authorized-cluster-endpoint).

Example:

```yaml
local_cluster_auth_endpoint:
  enabled: true
  fqdn: "FQDN"
  ca_certs: "BASE64_CACERT"
```

### Custom Network Plug-in

_Available as of v2.2.4_

You can add a custom network plug-in by using the [user-defined add-on functionality]({{<baseurl>}}/rke/latest/en/config-options/add-ons/user-defined-add-ons/) of RKE. You define any add-on that you want deployed after the Kubernetes cluster is deployed.

There are two ways that you can specify an add-on:

- [In-line Add-ons]({{<baseurl>}}/rke/latest/en/config-options/add-ons/user-defined-add-ons/#in-line-add-ons)
- [Referencing YAML Files for Add-ons]({{<baseurl>}}/rke/latest/en/config-options/add-ons/user-defined-add-ons/#referencing-yaml-files-for-add-ons)

For an example of how to configure a custom network plug-in by editing the `cluster.yml`, refer to the [RKE documentation.]({{<baseurl>}}/rke/latest/en/config-options/add-ons/network-plugins/custom-network-plugin-example)
