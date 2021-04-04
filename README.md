# azure-spn
Demoing Azure Service Principal (SPN) with RBAC.

```bash
az ad sp create-for-rbac -n "MyApp"
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
az ad sp create-for-rbac --skip-assignment
```

```bash
az ad sp create-for-rbac -n "MyApp" --role Contributor --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroup1} /subscriptions/{SubID}/resourceGroups/{ResourceGroup2}
```

--years
Number of years for which the credentials will be valid. Default: 1 year.

```bash
az ad sp delete --id 00000000-0000-0000-0000-000000000000
```
