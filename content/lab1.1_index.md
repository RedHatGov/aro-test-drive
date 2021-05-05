# Welcome

Welcome to ARO Test Drive!  In this guided tour, you will use the following technologies to deploy containerized software:

* [Azure Red Hat OpenShift (ARO)][1]
* [Azure Cosmos DB][2]
* [Red Hat Data Grid][8]
* [Quarkus][3]
* [Ploigos Software Factory][4]

**How much will this cost?**

The estimated cost is xyz.  You can see a breakdown of the cost in our pricing calculator export.

> TODO: Add pricing calculator export

## Prerequisites

Before you get started, you will need the following:

1. Pay-As-You-Go Azure Account or Enterprise Agreement
2. Owner Role, or Contributor + User Access Administator Roles
3. Resource Quota for at least 16 vCPU of Standard_D4s_v3 Series
4. Resource Quota for at least 24 vCPU of Standard_D8s_v3 Series

Please see Azure's [quota documentation][6] on how to increase resource limits.  For reference, see [OpenShift documentation][7] for the minimum requirements for all components.

**Can I use an Azure Free Account?**

No.  The Azure Free Account does not give you enough vCPU quota to provision an ARO cluster.  If you're using an Azure Free Account, then you must upgrade to a Pay-As-You-Go account or Enterprise Agreement and request to increase resource limits (see above)

[1]: https://azure.microsoft.com/en-us/services/openshift/
[2]: https://azure.microsoft.com/en-us/services/cosmos-db/
[3]: https://quarkus.io/
[4]: https://github.com/ploigos/ploigos-software-factory-operator
[5]: https://azure.microsoft.com/en-us/offers/ms-azr-0003p/
[6]: https://docs.microsoft.com/en-us/azure/azure-portal/supportability/per-vm-quota-request
[7]: https://docs.openshift.com/container-platform/4.6/installing/installing_azure/installing-azure-account.html#installation-azure-limits_installing-azure-account
[8]: https://www.redhat.com/en/technologies/jboss-middleware/data-grid