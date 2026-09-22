# Cost Management Plan

## Subscription Context

This Project uses my personal free-trial subscription.

Before creating or deleting resources, I check the active subscription:
    az account show --output table
If the wrong subscription is selected, I switch to the correct one:
    az account set --subscription "SUBSCRIPTION NAME"
This helps prevent changes to the wrong Azure environment.

# Naming Standard

Resource names follow this pattern:
    <type>-<workload>-<environment>-<region>-<number>
Example:
    rg-orion-dev-eus-01

- rg: resource group
- orion: project name
- dev: development environment
- eus: East US region
- 01: instance number

Consistent names make resources easier to identify

## Required Tags

Where supported, lab resources should have these tags:
| Tag | Value |
project: orion-cloud-lab
environment: dev
ownner: jan-lara
cost-center: learning
data-classification: synthetic

Tags help identify the purpose and owner of each resource.
Resource-group tags do not automatically apply to its reources.

## Budget

My planned monthly budget alert threshold is USD 5.

Email alerts are configured at:

- 50%: USD 2.50
- 80%: USD 4.00
- 100%: USD 5.00

The budget covers the whole subscription.

Budget alerts do not automatically stop spending.
Cost reporting can be delayed, so I will also check resource inventory and delete disposable resources after each paid lab.

I will prefer local exercises and free options whenever possible.

# Cleanup Procedure

1. Verify the active subscription.
2. List the resources in the lab resource group.
3. Save any lab evidence I need.
4. Identify resources that are no longer needed.
5. Delete disposable resources after checking their names and scope.
6. Confirm that deletion completed.
7. Review Cost Management for any remaining charges.

Inventory command:

    az resource list --resource-group rg-orion-dev-eus-01 --output table
If the entire lab resource group is no longer needed, delete it:
    az group delete --name rg-oirion-dev-eus-01

This deletes every resource inside the group.
I will review the target before confirming the deletion.

Verify that the resource group is gone:

    az group exists --name rg-orion-dev-eus-01

Expected result after deletion:

    false