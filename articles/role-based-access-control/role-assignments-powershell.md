---
title: Správa řízení přístupu na základě rolí (RBAC) pomocí prostředí Azure PowerShell | Microsoft Docs
description: Jak spravovat RBAC pomocí Azure Powershellu, včetně obsahující seznam rolí, přiřazení rolí a odstraňovat přiřazení role.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 9fe7e6d12c3a831b006107ae53ca37cd22e34d4c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Správa řízení přístupu na základě rolí pomocí Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](role-assignments-powershell.md)
> * [Azure CLI](role-assignments-cli.md)
> * [REST API](role-assignments-rest.md)

Pomocí řízení přístupu na základě rolí (RBAC) kterou definujte přístupu pro uživatele, skupiny a objekty služby přiřazení rolí v určitém rozsahu. Tento článek popisuje, jak můžete spravovat přístup pomocí prostředí Azure PowerShell.

## <a name="prerequisites"></a>Požadavky

Před prostředí PowerShell můžete použít ke správě RBAC, je třeba jeden z následujících akcí:

* [Prostředí PowerShell v prostředí cloudu Azure](/azure/cloud-shell/overview)
* [Prostředí Azure PowerShell 5.1.0 nebo novější](/powershell/azure/install-azurerm-ps)

## <a name="list-roles"></a>Seznam rolí

### <a name="list-all-available-roles"></a>Zobrazí seznam všech dostupných rolí

Rolím RBAC seznamu, které jsou k dispozici pro přiřazení a chcete-li prověřit operací, u kterých se udělit přístup, použijte [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>Seznam určité role

K zobrazení seznamu určité role, použijte [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition <role name>
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
AssignableScopes : {/}
```

### <a name="list-a-specific-role-in-json-format"></a>Seznam určité role ve formátu JSON

K zobrazení seznamu určité role ve formátu JSON, použijte [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition <role name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor" | ConvertTo-Json

{
    "Name":  "Contributor",
    "Id":  "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "IsCustom":  false,
    "Description":  "Lets you manage everything except access to resources.",
    "Actions":  [
                    "*"
                ],
    "NotActions":  [
                       "Microsoft.Authorization/*/Delete",
                       "Microsoft.Authorization/*/Write",
                       "Microsoft.Authorization/elevateAccess/Action"
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

### <a name="list-actions-of-a-role"></a>Seznam akcí role

K zobrazení seznamu akce pro určité role, použijte [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition <role name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action}
```

```azurepowershell
(Get-AzureRmRoleDefinition <role name>).Actions
```

```Example
PS C:\> (Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="see-who-has-access"></a>Zjistit, kdo má přístup

Přiřazení přístupu RBAC seznamu, použijte [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

### <a name="list-role-assignments-at-a-specific-scope"></a>Seznam přiřazení rolí na konkrétní rozsah

Zobrazí všechna přiřazení rolí pro zadané předplatné, skupinu prostředků nebo prostředek. Například pokud chcete zobrazit všech active přiřazení pro skupinu prostředků, použijte [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzureRmRoleAssignment -ResourceGroupName pharma-sales-projectforecast | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

### <a name="list-roles-assigned-to-a-user"></a>Seznam rolí přiřazen k uživateli

K zobrazení seznamu všech rolí, které jsou přiřazeny pro zadaného uživatele, použijte [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment -SignInName <user email>
```

```Example
PS C:\> Get-AzureRmRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

K zobrazení seznamu všech rolí, které jsou přiřazeny pro zadaného uživatele a role, které jsou přiřazeny do skupin, do kterých uživatel patří, použijte [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment -SignInName <user email> -ExpandPrincipalGroups
```

```Example
Get-AzureRmRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Seznam přiřazení rolí správce a coadmin classic služby

Seznam přiřazení přístupu pro správce classic předplatného a spolusprávci, použijte [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment):

```azurepowershell
Get-AzureRmRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>Udělení přístupu

### <a name="search-for-object-ids"></a>Vyhledejte ID objektů

Pokud chcete přiřadit roli, musíte určit objektu (uživatele, skupiny nebo aplikace) a obor.

Pokud si nejste jisti ID předplatného, najdete ji v **odběry** okno na portálu Azure, nebo můžete použít [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

ID objektu pro skupinu služby Azure AD, použijte [Get-AzureRmADGroup](/powershell/module/azurerm.resources/get-azurermadgroup):

```azurepowershell
Get-AzureRmADGroup -SearchString <group name in quotes>
```

ID objektu pro objekt zabezpečení služby Azure AD nebo aplikaci, použijte [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal):

```azurepowershell
Get-AzureRmADServicePrincipal -SearchString <service name in quotes>
```

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Přiřazení role k aplikaci v oboru předplatného

Pokud chcete udělit přístup k aplikaci v oboru předplatné, použijte [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```azurepowershell
New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>
```

```Example
PS C:\> New-AzureRmRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Přiřazení role uživatele v oboru skupiny prostředků

Pokud chcete udělit přístup na uživatele v oboru skupiny prostředků, použijte [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```azurepowershell
New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>
```

```Example
PS C:\> New-AzureRmRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Přiřazení role do skupiny v oboru prostředků

Pokud chcete udělit přístup do skupiny v oboru prostředků, použijte [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```azurepowershell
New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzureRmADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzureRmRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

## <a name="remove-access"></a>Odebrání přístupu

Chcete-li odebrat přístup pro uživatele, skupiny a aplikace, použijte [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment):

```azurepowershell
Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>
```

```Example
PS C:\> Remove-AzureRmRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast
```

## <a name="list-custom-roles"></a>Vlastní role seznamu

K zobrazení seznamu rolí, které jsou k dispozici pro přiřazení v oboru, použijte [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) příkaz.

Následující příklad vypíše všechny role, které jsou k dispozici pro přiřazení ve vybraném předplatném.

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

Následující příklad uvádí jenom vlastní role, které jsou k dispozici pro přiřazení ve vybraném předplatném.

```azurepowershell
Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Pokud vybrané předplatné není v `AssignableScopes` rolí, vlastní role nebude v seznamu uveden.

## <a name="create-a-custom-role"></a>Vytvořit vlastní roli

Chcete-li vytvořit vlastní roli, použijte [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) příkaz. Strukturování roli, dva způsoby použití `PSRoleDefinition` objekt nebo šablonu JSON. 

### <a name="get-operations-for-a-resource-provider"></a>Získejte operace pro poskytovatele prostředků

Když vytvoříte vlastní role, je důležité vědět, všechny možné operace od poskytovatelů prostředků.
Můžete zobrazit seznam [operace poskytovatele prostředků](resource-provider-operations.md) nebo můžete použít [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) získat tyto informace.
Například pokud chcete zkontrolovat všechny operace, které jsou k dispozici pro virtuální počítače, použijte tento příkaz:

```azurepowershell
Get-AzureRMProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-role-with-psroledefinition-object"></a>Umožňuje vytvořit roli s objektem PSRoleDefinition

Pokud používáte PowerShell můžete vytvořit vlastní roli, můžete použít jednu z [předdefinované role](built-in-roles.md) jako počáteční bod, nebo můžete začít úplně od začátku. V prvním příkladu v této části začíná předdefinovaná role a pak ho přizpůsobí s další oprávnění. Upravit atributy, které se přidat `Actions`, `NotActions`, nebo `AssignableScopes` a potom uložte změny jako novou roli.

Následující příklad začíná [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) předdefinovaná role, chcete-li vytvořit vlastní roli s názvem *operátor virtuální počítač*. Nová role uděluje přístup ke všem operacím čtení z *Microsoft.Compute*, *Microsoft.Storage*, a *Microsoft.Network* zprostředkovatelé a uděluje přístup k prostředkům na spuštění, restartování a monitorování virtuálních počítačů. Vlastní role lze použít ve dvou odběry.

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzureRmRoleDefinition -Role $role
```

Následující příklad ukazuje další způsob vytvoření *virtuální počítač operátor* vlastní role. Začne tím, že vytvoříte nový objekt PSRoleDefinition. Akce operace, které jsou určené v `perms` proměnné a nastavena `Actions` vlastnost. `NotActions` Načtením je hodnota nastavena `NotActions` z [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) předdefinovaná role. Vzhledem k tomu [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) nemá žádné `NotActions`, tento řádek není vyžadováno, ale ukazuje, jak můžete načíst informace z jiné role.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read','Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzureRmRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzureRmRoleDefinition -Role $role
```

### <a name="create-role-with-json-template"></a>Vytvoření role pomocí šablony JSON

Šablonu JSON slouží jako zdroj definice pro vlastní roli. Následující příklad vytvoří vlastní role, která umožňuje přístup pro čtení na úložiště a výpočetní prostředky, přístup k podporovat, a přidá do dvou odběry této role. Vytvoření nového souboru `C:\CustomRoles\customrole1.json` s následující příklad. Id musí být nastavena na `null` na vytvoření počáteční role jako nové ID je generován automaticky. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Přidejte roli k předplatným, spusťte následující příkaz prostředí PowerShell:

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>Upravit vlastní roli

Podobně jako u vytváření vlastní roli, můžete upravit existující vlastní role pomocí `PSRoleDefinition` objekt nebo šablonu JSON.

### <a name="modify-role-with-psroledefinition-object"></a>Upravit roli s objektem PSRoleDefinition

Chcete-li upravit vlastní roli, nejprve použijte [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) příkaz načíst definici role. Za druhé proveďte požadované změny k definici role. Nakonec použijte [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) příkaz pro uložení definice upravené role.

Následující příklad přidá `Microsoft.Insights/diagnosticSettings/*` operace *virtuální počítač operátor* vlastní role.

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

Následující příklad přidá do přiřaditelnými obory z předplatného Azure *virtuální počítač operátor* vlastní role.

```azurepowershell
Get-AzureRmSubscription -SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzureRmSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

### <a name="modify-role-with-json-template"></a>Upravit role pomocí šablony JSON

Pomocí předchozího šablony JSON, můžete snadno upravit existující vlastní roli přidat nebo odebrat akce. Aktualizovat šablonu JSON a přidejte čtení akce pro sítě, jak je znázorněno v následujícím příkladu. Definice uvedené v šabloně nepoužívají kumulativně existující definice, což znamená, že role zobrazí přesně tak, jak zadáte v šabloně. Také musíte aktualizovat pole Id s ID role. Pokud si nejste jisti, co je tato hodnota, můžete použít [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) rutiny získat tyto informace.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Pokud chcete aktualizovat existující roli, spusťte následující příkaz prostředí PowerShell:

```azurepowershell
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Odstranit vlastní roli

Chcete-li odstranit vlastní roli, použijte [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) příkaz.

Následující příklad odebere *virtuální počítač operátor* vlastní role.

```azurepowershell
Get-AzureRmRoleDefinition "Virtual Machine Operator"
Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="see-also"></a>Další informace najdete v tématech

* [Použití Azure PowerShellu s Azure Resource Managerem](../azure-resource-manager/powershell-azure-resource-manager.md)

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
