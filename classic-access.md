---
copyright:
  years: 2018, 2020
lastupdated: "2020-07-16"

keywords: classic, access, classic access, VRF, peering

subcollection: vpc

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:important: .important}
{:download: .download}
{:table: .aria-labeledby="caption"}
{:DomainName: data-hd-keyref="DomainName"}
{:external: target="_blank" .external}

# Setting up access to classic infrastructure
{: #setting-up-access-to-classic-infrastructure}
[comment]: # (linked help topic)

You can set up access from a VPC to your {{site.data.keyword.cloud}} classic infrastructure, including Direct Link connectivity. Only one VPC per region can communicate with classic resources.
{: shortdesc}

When you set up a VPC for classic access, every virtual server instance or bare metal server without a public interface in your classic account can send and receive packets to and from the classic access VPC. Firewalls, gateways, network ACLs, or security groups can filter some or all of this traffic. As a best practice, allow only traffic that's required for your applications to function properly.

For virtual server instances and bare metal instances on the classic infrastructure that use a public interface, you must add a route that points back to your classic-enabled VPC. This route must include the subnets of your classic-enabled VPC as a destination. The route must also point to a gateway address for traffic that leaves the private interface of the host as the next hop.
{: important}

## Prerequisites
{: #vpc-prerequisites}

 Your classic account must be enabled for Virtual Router Forwarding (VRF).
   * If you already have Direct Link on your account, you're all set. If you don't have a Direct Link gateway, see [Getting started with Direct Link (2.0)](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl) to set up and provision a Direct Link gateway.
   * If your account is not VRF-enabled, open a ticket to request "VRF Migration" for your account. See [Converting to VRF](#vrf-conversion) to learn more about the conversion process.
 
All subnets in a classic access VPC are shared into the classic infrastructure VRF, which uses IP addresses in the `10.0.0.0/8` space. To avoid IP address conflicts, don't use IP addresses in the `10.0.0.0/14`, `10.200.0.0/14`, `10.198.0.0/15`, and `10.254.0.0/16` blocks in your classic access VPC. Also, don't use addresses from your classic infrastructure subnets. To view the list of your classic infrastructure subnets, see [View all Subnets](/docs/subnets?topic=subnets-view-all-subnets).
{: important}

## Creating a classic access VPC
{: #create-a-classic-access-vpc}

You can create a classic access VPC by using the {{site.data.keyword.cloud_notm}} console, CLI, or API.

A VPC must be set up for classic access when it's created: you cannot update a VPC to add or remove classic access.
{: important}

### Using the {{site.data.keyword.cloud_notm}} console to create a classic access VPC
{: #create-a-classic-access-vpc-from-the-user-interface}

On the **New virtual private cloud** page, select **Enable access to classic resource** under **Classic access**.

### Using the CLI to create a classic access VPC
{: #create-a-classic-access-vpc-using-the-cli}

Use the flag `--classic-access` when you create the VPC, for example:

```
ibmcloud is vpc-create my-access-vpc --classic-access
```
{: pre}


### Using the API to create a classic access VPC
{: #create-a-classic-access-vpc-using-the-api}

Pass in the `classic_access` parameter when you create the VPC, for example:

```bash
curl -X POST "$rias_endpoint/v1/vpcs?version=$api_version&generation=2" \
  -H "Authorization: $iam_token" \
  -d '{
        "name": "my-access-vpc",
        "classic_access": true
      }'
```
{: pre}


### Classic access VPC default address prefixes
{: #classic-access-default-address-prefixes}

When a classic access VPC is created, the default address prefixes are assigned based on the zone as follows:

Zone         | Address Prefix
---------------|---------------
`us-south-1`   | `172.16.0.0/18`
`us-south-2`   | `172.16.64.0/18`
`us-south-3`   | `172.16.128.0/18`
`us-east-1`    | `172.17.0.0/18`
`us-east-2`    | `172.17.64.0/18`
`us-east-3`    | `172.17.128.0/18`
`eu-gb-1`      | `172.18.0.0/18`
`eu-gb-2`      | `172.18.64.0/18`
`eu-gb-3`      | `172.18.128.0/18`
`eu-de-1`      | `172.19.0.0/18`
`eu-de-2`      | `172.19.64.0/18`
`eu-de-3`      | `172.19.128.0/18`
`jp-tok-1`     | `172.20.0.0/18`
`jp-tok-2` 	   | `172.20.64.0/18`
`jp-tok-3` 	   | `172.20.128.0/18`
{: caption="Table 1. Classic access VPC address prefixes by zone" caption-side="top"}

To prevent address prefixes from being created, you can add the `"address_prefix_management": "manual"` parameter when you create the VPC by using the API:

```bash
curl -H "Authorization:$iam_token" "$iaas_endpoint/v1/vpcs?generation=2&version=$api_version" -X POST -d '{ "name": "my-access-vpc", "address_prefix_management": "manual", "classic_access": true}'
```
{: pre}

## Converting your account to VRF
{: #vrf-conversion}

The VRF conversion process involves a network disruption while the VLANs and their subnets are detached from the ACL backbone and then attached to the _Customer VRF_. This process results in a few moments of packet loss for traffic entering or exiting the VLANs. Packets within the VLAN continue to flow. In the cases where a network gateway, such as a FortiGate Security Appliance or Virtual Router Appliance is involved, no disruption occurs among the VLANs attached to that gateway. The servers see no network outage themselves, and most workloads automatically recover when the traffic flow resumes. The total duration of the disruption depends on the extent of the tenant’s topology, that is, the number of subnets, VLANs, and pods that your tenancy includes.

During migration, the VLANs are disconnected from the backbone and reconnected to the _Customer VRF_. The duration of disruption varies, depending on the quantity of VLANs, PODs, and data centers involved. Traffic among VLANs is disrupted, yet the servers stay connected to the network. The application may or may not be affected, depending on its sensitivity to packet loss.

### How to initiate VRF conversion
{: #how-you-can-initiate-the-conversion}

To request conversion of your account to VRF, follow these steps:

1. [Open a support case](https://cloud.ibm.com/unifiedsupport/cases/add){: external} through the {{site.data.keyword.cloud_notm}} console.
1. Select "Technical" for the type of support that is needed and "VPC Network" for the category.
1. Enter "Convert account to VRF for VPC Classic Access" as the subject.
1. For the description, enter the following text:
   "We are requesting that account _your account number_ is moved to its own VRF. We understand the risks and approve the change. Please reply with the scheduled window(s) of time where this change will be made so we can prepare for the migration."

Migration is completed by the {{site.data.keyword.cloud_notm}} Network Engineering team. No other information is required from you, except an agreed-to schedule. Typically, packet loss might last 15 - 30 minutes, depending on the complexity of your account. It might be longer if your account has legacy Direct Link connections.

## Limitations
{: #vpc-limitations}

* Only private networks (also known as backend networks) in classic infrastructure can be connected to your VPC.
* Only subnets allocated to your classic infrastructure with {{site.data.keyword.cloud_notm}} provisioning systems can be connected to your VPC.
* Only one VPC per region can be enabled for classic access.
* If your classic infrastructure includes an imported default route from Direct Link, the imported default route is used by your Classic Access VPC. In this scenario, the public gateway and floating IPs in your Classic Access VPC do not provide internet access. When the default route is no longer imported via Direct Link, then public gateways and floating IPs once again provide internet access.
