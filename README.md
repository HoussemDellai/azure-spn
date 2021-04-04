# azure-spn
Demoing Azure Service Principal (SPN) with RBAC.


By default, this command (_az ad sp create-for-rbac_) assigns the 'Contributor' role to the service principal at the subscription scope. To reduce your risk of a compromised service principal, use --skip-assignment to avoid creating a role assignment, then assign a more specific role and narrow the scope to a resource or resource group. 
