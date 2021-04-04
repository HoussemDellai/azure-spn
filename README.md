# azure-spn
Demoing Azure Service Principal (SPN) with RBAC.

This workshop is using Bash in Azure Cloud Shell.

```bash
az ad sp create-for-rbac -n "MyApp"

Changing "MyApp" to a valid URI of "http://MyApp", which is the required format used for service principal names
In a future release, this command will NOT create a 'Contributor' role assignment by default. 
If needed, use the --role argument to explicitly create a role assignment.
Creating 'Contributor' role assignment under scope '/subscriptions/17b12858-xxxx-xxxx-xxxx-a06fdae23428'
{
  "appId": "ced608dc-xxxx-4c22-835f-9379363e0d50",
  "displayName": "MyApp",
  "name": "http://MyApp",
  "password": "dr5cluFZ~xX_5x18oNv6n.SW.rJk~xx~ng",
  "tenant": "558506eb-xxxx-4ef3-xxxx-ad55c555e729"
}
```

By default, this command (_az ad sp create-for-rbac_) assigns the 'Contributor' role to the service principal at the subscription scope.

Check your Azure subscription from portal, under Access control (IAM), Role assignments, and you should see the SPn have now Contributor role over your subscription.

To reduce your risk of a compromised service principal, use --skip-assignment to avoid creating a role assignment, then assign a more specific role and narrow the scope to a resource or resource group. 

Lets first create the SPN with no assignments and no roles:

```bash
az ad sp create-for-rbac -n "MySPN" --skip-assignment
```

Later we'll need the SPN credentials, to make it easier to get these, we'll use environment variables:

```bash
SPN=$(az ad sp create-for-rbac -n "MySPN" --skip-assignment -o json)
```

Then let's display the credentials using jq tool, which is already installed in Azure Cloud Shell, to display the SPN and to extract some specific fields like appId:

```bash
echo $SPN | jq
echo $SPN | jq -r '.appId'
```

We can aasign roles to the Service Principal either using the Azure Portal or using the Azure CLI. 

With the first solution, from the Azure portal you can navigate to your resource (resource group for example), then Access control (IAM), Role assignments and then you can assign a role (like Reader) to your SPN (search with your SPN name).

The second solution is using command line. We'll create a new resource group.

```bash
az group create -n spn-demo2-rg
SCOPE="/subscriptions/<YOU_SUBSCRIPTION_ID>/resourceGroups/spn-demo2-rg"
```

Then we use the resource group to assign role Contributor for the SPN:

```bash
az role assignment create --role Contributor \
                          --assignee $(echo $SPN | jq -r '.appId') \
                          --scope $SCOPE
```

Now you can check the Access control (IAM) in your resource group from Azure portal.

Note that you can also create a Service Principal with role Contributor and assign the role to multiple resources like 2 resource groups at once:

```bash
az ad sp create-for-rbac -n "MyApp" 
                         --role Contributor 
                         --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroup1} /subscriptions/{SubID}/resourceGroups/{ResourceGroup2}
```

Note alse thay you can use the parameter **--years** as number of years for which the credentials will be valid. Default: 1 year.

Azure users typically uses their Identity (email and password) to connect to Azure. But machines (like DevOps build agents) will use the SPN.

Login to Azure using SPN:

```bash
az login --service-principal --username $(echo $SPN | jq -r '.appId') \
                             --password $(echo $SPN | jq -r '.password') \
                             --tenant   $(echo $SPN | jq -r '.tenant')
```

Now if we try to get the resource groups we'll see only the one with SPN assigned:

```bash
az group list -o table
Name          Location    Status
------------  ----------  ---------
spn-demo2-rg  westeurope  Succeeded
```

To login back to Azure CLI using your personal identity, just use 

```bash
az login
```

Finally, we'll got to delete the SPN we used here.

```bash
az ad sp delete --id $(echo $SPN | jq -r '.appId')
```

More details:
https://docs.microsoft.com/en-us/cli/azure/ad/sp?view=azure-cli-latest
https://docs.microsoft.com/en-us/cli/azure/role/assignment?view=azure-cli-latest
https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal
