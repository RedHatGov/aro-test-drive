# Setup

Let's start by downloading the Azure CLI `az`.  Navigate to this [link][1] to download the binary.

Once downloaded, login to your account:

```bash
az login
```

Create an ARO cluster.  You have two options:

1. Read and follow the [official tutorial][2].
2. Watch and follow the [demo][3].

**Note: The Red Hat Pull Secret and custom domain is optional**

Follow the [official tutorial][4] to connect to the cluster.  Download and login using the `oc` CLI.

[1]: https://docs.microsoft.com/en-us/cli/azure/install-azure-cli
[2]: https://docs.microsoft.com/en-us/azure/openshift/tutorial-create-cluster
[3]: https://www.youtube.com/watch?v=OTC1SLMjKaA
[4]: https://docs.microsoft.com/en-us/azure/openshift/tutorial-connect-cluster#connect-to-the-cluster