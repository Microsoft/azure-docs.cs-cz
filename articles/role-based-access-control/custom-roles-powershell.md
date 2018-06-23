---
title: Vytvořit vlastní role pomocí Azure PowerShell | Microsoft Docs
description: Naučte se vytvářet vlastní role pro řízení přístupu na základě role (RBAC) pomocí prostředí Azure PowerShell. To zahrnuje seznam, vytvářet, aktualizovat a odstraňovat vlastní role.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6205198d3f9c4ec5bfa2311014cf9b90dcade6dc
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320517"
---
# <a name="create-custom-roles-using-azure-powershell"></a>Vytvořit vlastní role pomocí Azure PowerShell

Pokud [předdefinované role](built-in-roles.md) nemáte splnili specifické požadavky vaší organizace, můžete vytvořit vlastní role. Tento článek popisuje, jak vytvářet a spravovat vlastní role pomocí Azure PowerShell.

## <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit vlastní role, je třeba:

- Oprávnění k vytvoření vlastních rolí, například [vlastníka](built-in-roles.md#owner) nebo [správce přístupu uživatelů](built-in-roles.md#user-access-administrator)
- [Prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) nainstalovány místně

## <a name="list-custom-roles"></a>Vlastní role seznamu

K zobrazení seznamu rolí, které jsou k dispozici pro přiřazení v oboru, použijte [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) příkaz. Následující příklad vypíše všechny role, které jsou k dispozici pro přiřazení ve vybraném předplatném.

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

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Vytvoření vlastních rolí s objektem PSRoleDefinition

Pokud používáte PowerShell můžete vytvořit vlastní roli, můžete použít jednu z [předdefinované role](built-in-roles.md) jako počáteční bod, nebo můžete začít úplně od začátku. V prvním příkladu v této části začíná předdefinovaná role a pak ho přizpůsobí s další oprávnění. Upravit atributy, které se přidat `Actions`, `NotActions`, nebo `AssignableScopes` a potom uložte změny jako novou roli.

Následující příklad začíná [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) předdefinovaná role, chcete-li vytvořit vlastní roli s názvem *operátor virtuální počítač*. Nová role uděluje přístup ke všem operacím čtení z *Microsoft.Compute*, *Microsoft.Storage*, a *Microsoft.Network* zprostředkovatelé a uděluje přístup k prostředkům spuštění , restartujte a monitorování virtuálních počítačů. Vlastní role lze použít ve dvou odběry.

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

Následující příklad ukazuje další způsob vytvoření *virtuální počítač operátor* vlastní role. Začne tím, že vytvoříte novou `PSRoleDefinition` objektu. Akce operace, které jsou určené v `perms` proměnné a nastavena `Actions` vlastnost. `NotActions` Načtením je hodnota nastavena `NotActions` z [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) předdefinovaná role. Vzhledem k tomu [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) nemá žádné `NotActions`, tento řádek není vyžadováno, ale ukazuje, jak můžete načíst informace z jiné role.

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

### <a name="create-a-custom-role-with-json-template"></a>Vytvořit vlastní role pomocí šablony JSON

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

## <a name="update-a-custom-role"></a>Aktualizovat vlastní role

Podobně jako u vytváření vlastní roli, můžete upravit existující vlastní role pomocí `PSRoleDefinition` objekt nebo šablonu JSON.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Aktualizovat vlastní roli s objektem PSRoleDefinition

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

### <a name="update-a-custom-role-with-a-json-template"></a>Aktualizovat vlastní role pomocí šablony JSON

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

## <a name="next-steps"></a>Další postup

- [Kurz: Vytvoření vlastní role pomocí Azure PowerShell](tutorial-custom-role-powershell.md)
- [Vlastní role v Azure](custom-roles.md)
- [Operace zprostředkovatele prostředků v Azure Resource Manager](resource-provider-operations.md)
