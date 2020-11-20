---

copyright:
  years: 2020
lastupdated: "2020-11-19"

keywords: custom routes

subcollection: vpc

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}

# About routing tables and routes
{: #about-custom-routes}

{{site.data.keyword.cloud}} Virtual Private Cloud (VPC) automatically generates a default routing table for the VPC to manage traffic in the zone. By default, this routing table is empty. You can add routes to the default routing table, or create one or more custom routing tables and then add routes to these tables. For example, if you want a specialized routing policy for a specific subnet, you can create a routing table and associate it with one or more subnets. However, if you want to change the default routing policy, which affects all subnets that use the default routing table, then you should add routes to the default routing table. 
{:shortdesc}

The default routing table functions the same as other routing tables, except that it is created automatically, and is used when you create a subnet without specifying a routing table. 
{: note}

You can define routes within any routing table to shape traffic the way that you want. Each subnet has one routing table assigned to it, which is responsible for managing the subnet's traffic. You can change the routing table that a subnet uses to manage its traffic at any time.

This service also allows the use of Network Functions Virtualization (NFV) for advanced networking services, such as third-party routing, firewalls, local/global load balancing, web application firewalls, and more. Custom routing tables are also currently being integrated in {{site.data.keyword.cloud_notm}} services. 

## Egress and ingress routing
{: #egress-ingress-overview}

When you create a routing table, you can select one of the following traffic types:

* **Egress** routes control traffic, which originates within a private network and travels towards the public internet, or to another VM in same zone in different subnet.
* **Ingress** routes enable you to customize routes on incoming traffic to a VPC from traffic sources external to the VPC's availability zone (IBM Cloud Direct Link 2.0, IBM Cloud Transit Gateway, or another availability zone in same VPC). You must choose at least one traffic source to enable ingress routing.

   Only one custom routing table can be associated with a particular ingress traffic source. However, you can use different routing tables for different traffic sources. For example, routing table A might use Transit Gateway and VPC Zone, and routing table B use Direct Link 2.0.
   {: note}

## System routing table

Use the system routing table for routing traffic when no matching route is found in the custom routing table that is associated with the subnet where the traffic is egressing. A system routing table is maintained for each VPC. A VPC can have a presence in multiple zones, and the VPC's system routing table is different in each zone. For ingress routing, the system routing table contains only routes to each network interface in the VPC’s zone.

## Limitations and guidelines
{: #limitations-custom-routes}

The following limitations and guidelines apply to {{site.data.keyword.cloud_notm}} Custom Routes for VPC:

* You can use a maximum of 14 unique prefix lengths per custom routing table. You can have multiple routes with the same prefix that counts as only one unique prefix. For example, you might have multiple routes with a `/28` prefix. This counts as one unique prefix.
* Reachability of a next hop IP address in a custom route is not a determining factor in whether the route is used for forward traffic. This can cause issues when multiple routes with the same prefix (but different next hop IP addresses) are used, as traffic to unreachable next hop IP addresses might not be delivered.
* When creating a route for a static, route-based VPN connection, you must enter the VPN connection ID for the next hop. The VPN gateway must be in the same zone as the subnet to which the routing table is associated with. A VPN gateway as the next hop in a different zone than the subnet that is associated with the routing table is not recommended.
* A custom routing table that contains custom routes with a next hop that is associated with a VPN connection cannot be associated with an ingress traffic source.
* For egress custom routes, when you add a destination route, you must select a zone. However, the next hop doesn't have to be in the same zone. For ingress custom routes, the next hop must be in the same zone.
* The implicit router performs equal-cost multi-path (ECMP) routing (multiple routes with the same destination, but different next hop addresses) with the following limitations:

   * This only applies to routes with an action of deliver.
   * Only two identical destination routes are permitted per zone, and each must have different next hop addresses.
   * When ECMP is used, the return path might not take the same path.

* Custom routes in a custom routing table associated with an ingress traffic source, and with an action of **deliver**, must have a next hop IP contained by one of the root prefixes of the VPC in the availability zone where the route is added. In addition, the next hop IP must be configured on a virtual server interface in the VPC and availability zone.  
* Ingress traffic from a particular traffic source is routed using the routes in the custom routing table that is associated with that traffic source.
* Currently, you cannot use a custom routing table for both ingress (attached to a traffic source) and egress (attached to a subnet) traffic. In addition, the default custom routing table cannot be associated with an ingress traffic source.

## Related links
{: #related-links-custom-routes}

These links provide additional information about {{site.data.keyword.cloud_notm}} Custom Routes for VPC.

* [VPC CLI reference](/docs/vpc?topic=vpc-infrastructure-cli-plugin-vpc-reference#custom-routes-section)
* [VPC API reference](https://{DomainName}/apidocs/vpc)
* [Activity Tracker events](/docs/vpc?topic=vpc-at-events#events-custom-routes)
* [Required permissions for routing tables and routes](/docs/vpc?topic=vpc-resource-authorizations-required-for-api-and-cli-calls)
* [Quotas and service limits](/docs/vpc?topic=vpc-quotas#routing-tables-routes-quotas)
* Ingress traffic sources: IBM Cloud [Direct Link 2.0](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl) and [Transit Gateway](/docs/transit-gateway?topic=transit-gateway-getting-started)
