# azure-spn
Demoing Azure Service Principal (SPN) with RBAC.

```bash
az ad sp create-for-rbac -n "MyApp"

Changing "MyApp" to a valid URI of "http://MyApp", which is the required format used for service principal names
In a future release, this command will NOT create a 'Contributor' role assignment by default. If needed, use the --role argument to explicitly create a role assignment.
Creating 'Contributor' role assignment under scope '/subscriptions/17b12858-xxxx-xxxx-xxxx-a06fdae23428'
{
  "appId": "ced608dc-xxxx-4c22-835f-9379363e0d50",
  "displayName": "MyApp",
  "name": "http://MyApp",
  "password": "dr5cluFZ~xX_5x18oNv6n.SW.rJk~xx~ng",
  "tenant": "558506eb-xxxx-4ef3-xxxx-ad55c555e729"
}
```

By default, this command (_az ad sp create-for-rbac_) assigns the 'Contributor' role to the service principal at the subscription scope. To reduce your risk of a compromised service principal, use --skip-assignment to avoid creating a role assignment, then assign a more specific role and narrow the scope to a resource or resource group. 

```bash
az ad sp create-for-rbac -n "MyApp-no-roles" --skip-assignment
```

We can aasign roles to the Service Principal:

```bash
az role assignment create --role "Owner" --assignee "Jhon.Doe@Contoso.com" 
```

Let's create a Service Priincipal with role Contributor and assign the role to scopes which are 2 resource groups:

```bash
az ad sp create-for-rbac -n "MyApp" --role Contributor --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroup1} /subscriptions/{SubID}/resourceGroups/{ResourceGroup2}
```

Login to Azure using SPN:

```bash
az login --service-principal -u <app-name-or-url> -p <password-or-cert> --tenant <tenant>
```

--years
Number of years for which the credentials will be valid. Default: 1 year.

```bash
az ad sp delete --id 00000000-0000-0000-0000-000000000000
```
