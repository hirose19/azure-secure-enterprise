# Network Design

## Purpose

This lab separates a web application into web, application, and data tiers. Each tier has its own subnet and network security group (NSG). NSG rules control which new inbound connections each tier accepts.

## Network Layout

Resource group: rg-orion-dev-eus-01
Region: eastus
Virtual network: vnet-orion-spoke-dev-eus-01

Tier |   Subnet   | Address range | Attached NSG
Web     snet-web    10.20.10.0/24   nsg-web
App     snet-app    10.20.20.0/24   nsg-app
Data    snet-data   10.20.30.0/24   nsg-data

## Allowed Inbound Connections

Source     |  Destination  | Protocol  | Destination port   | Priority
Internet        Web subnet      TCP         443                 100
Web subnet      App subnet      TCP         8080                100
App subnet      Data subnet     TCP         1433                100

Each NSG also has a priority 200 rule denying all other inbound traffic. The priority 100 allow rule is evaluated first.

These ports represent the planned application design. No web server, application server, or database has been deployed in this lab yet.

## Why Separate Subnets Are Not Enough.

Separate subnets do not automatically block traffic between tiers. The default NSG rules allow communication within the VNet. We attached an NSG to each subnet and added custom allow and deny rules so only the intended new inbound connections are permitted.

## Outbound Traffic and Stateful Replies

We have not created custom outbound rules. The default AllowVnetOutBound rule permists outbound traffic between our tiers. The destination tier's inbound rules must also allow a new connection.

For example, web-to-app traffic on TCP 8080 passes both checks. Web-to-data traffic on TCP 1433 passes the outbound check but is denied by the data subnet's inbound rules.

NSGs are stateful: replies to an allowed connection are automatically permitted. A reply does not require a separate reverse-direction allow rule. A new connection is evaluated separately.

The default AllowInternetOutBound rule also remains in place. Our current configuration does not restrict outbound internet traffic at the NSG level.

## Verification and Current Limitations

Verified using Azure CLI:
- Each subnet has its intended NSG attached.
- Each NSG has the intended inbound allow rule at priority 100.
- Each NSG has a deny-all-other-inbound rule at priority 200.

These checks verify configuration, not live connectivity.
No application workloads have been deployed, so connection testing has not yet been performed.

## Hub VNet and Peering

Hub VNet: vnet-orion-hub-dev-eus-01
Hub address space: 10.10.0.0/16

The hub and spoke address spaces do not overlap.

Local VNet  |   Peering Name    | Remote VNet   |   Verified State
Hub           peer-hub-to-spoke     Spoke           Connected
Spoke         peer-spoke-to-hub     Hub             Connected

VNet access is enabled on both peering configurations.
Peering provides private network connectivity, but NSG rules still apply.

The data subnet only allows new inbound connections from the app subnet on TPC 1433. New connections from the hub are denied by the priority 200 rule.

Peering is not transitive. Adding another spoke connected to the hub would not automatically provide connectivity between the two spokes.

Both peering states were verified using Azure CLI. Workload connectivity has not been tested because no VMs have been deployed.

The VNets themselves are free. Traffic transferred through VNet peering is billed by data volume.