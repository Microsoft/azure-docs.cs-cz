---
title: Vytvoření nebo aktualizace vlastních rolí pro prostředky Azure pomocí Azure PowerShellu
description: Zjistěte, jak vypsat, vytvořit, aktualizovat nebo odstranit vlastní role pomocí řízení přístupu na základě rolí (RBAC) pro prostředky Azure pomocí Azure PowerShellu.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3c72e04ff7a08fecc2ef352a5879898c4c6d41c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062275"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-powershell"></a>Vytvoření nebo aktualizace vlastních rolí pro prostředky Azure pomocí Azure PowerShellu

> [!IMPORTANT]
> Přidání skupiny `AssignableScopes` pro správu do aplikace je aktuálně ve verzi Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud [předdefinované role pro prostředky Azure](built-in-roles.md) nesplňují specifické potřeby vaší organizace, můžete si vytvořit vlastní role. Tento článek popisuje, jak vypsat, vytvořit, aktualizovat nebo odstranit vlastní role pomocí Azure PowerShellu.

Podrobný návod, jak vytvořit vlastní roli, najdete [v tématu Kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure PowerShellu](tutorial-custom-role-powershell.md).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit vlastní role, potřebujete:

- Oprávnění k vytváření vlastních rolí, například [Vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) nebo [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Výpis vlastních rolí

Chcete-li vypsat role, které jsou k dispozici pro přiřazení v oboru, použijte příkaz [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition) Následující příklad uvádí všechny role, které jsou k dispozici pro přiřazení ve vybraném předplatném.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
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

V následujícím příkladu jsou uvedeny pouze vlastní role, které jsou k dispozici pro přiřazení ve vybraném předplatném.

```azurepowershell
Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Pokud vybrané předplatné není v `AssignableScopes` roli, vlastní role nebude uvedena.

## <a name="list-a-custom-role-definition"></a>Vypsat vlastní definici role

Chcete-li vypsat vlastní definici role, použijte [get-azroledefinition](/powershell/module/az.resources/get-azroledefinition). Jedná se o stejný příkaz, který používáte pro předdefinovanou roli.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

V následujícím příkladu jsou uvedeny pouze akce role:

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

"Microsoft.Storage/*/read",
"Microsoft.Network/*/read",
"Microsoft.Compute/*/read",
"Microsoft.Compute/virtualMachines/start/action",
"Microsoft.Compute/virtualMachines/restart/action",
"Microsoft.Authorization/*/read",
"Microsoft.ResourceHealth/availabilityStatuses/read",
"Microsoft.Resources/subscriptions/resourceGroups/read",
"Microsoft.Insights/alertRules/*",
"Microsoft.Insights/diagnosticSettings/*",
"Microsoft.Support/*"
```

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Chcete-li vytvořit vlastní roli, použijte příkaz [New-AzRoleDefinition.](/powershell/module/az.resources/new-azroledefinition) Existují dvě metody strukturování role pomocí `PSRoleDefinition` objektu nebo šablony JSON. 

### <a name="get-operations-for-a-resource-provider"></a>Získání operací pro poskytovatele prostředků

Při vytváření vlastních rolí je důležité znát všechny možné operace od poskytovatelů prostředků.
Můžete zobrazit seznam [operací zprostředkovatele prostředků](resource-provider-operations.md) nebo můžete tyto informace získat pomocí příkazu [Get-AzProviderOperation.](/powershell/module/az.resources/get-azprovideroperation)
Pokud například chcete zkontrolovat všechny dostupné operace pro virtuální počítače, použijte tento příkaz:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Vytvoření vlastní role s objektem PSRoleDefinition

Při použití Prostředí PowerShell k vytvoření vlastní role, můžete použít jednu z [předdefinovaných rolí](built-in-roles.md) jako výchozí bod, nebo můžete začít od začátku. První příklad v této části začíná předdefinovanou rolí a pak ji přizpůsobí s více oprávněními. Upravte `Actions`atributy, chcete-li přidat , `NotActions`nebo `AssignableScopes` které chcete, a uložte změny jako novou roli.

Následující příklad začíná předdefinovanou rolí [přispěvatele virtuálního počítače](built-in-roles.md#virtual-machine-contributor) a vytvoření vlastní role s názvem *Operátor virtuálního počítače*. Nová role uděluje přístup ke všem operacím čtení *microsoft.compute*, *Microsoft.Storage*a *Microsoft.Network* zprostředkovatelů prostředků a uděluje přístup ke spuštění, restartování a monitorování virtuálních počítačů. Vlastní roli lze použít ve dvou předplatných.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
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
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

Následující příklad ukazuje jiný způsob, jak vytvořit vlastní roli *operátorvirtuálního počítače.* Začíná vytvořením nového `PSRoleDefinition` objektu. Akce operace jsou určeny `perms` v proměnné `Actions` a nastavit na vlastnost. Vlastnost `NotActions` je nastavena `NotActions` čtením z integrované role [přispěvatele virtuálního počítače.](built-in-roles.md#virtual-machine-contributor) Vzhledem k `NotActions`tomu, že přispěvatel [virtuálního počítače](built-in-roles.md#virtual-machine-contributor) žádné nemá , tento řádek není povinný, ale ukazuje, jak lze informace načíst z jiné role.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Vytvoření vlastní role pomocí šablony JSON

Šablonu JSON lze použít jako zdrojovou definici vlastní role. Následující příklad vytvoří vlastní roli, která umožňuje přístup pro čtení k úložišti a výpočetníprostředky, přístup k podpoře a přidá tuto roli do dvou předplatných. Vytvořte nový `C:\CustomRoles\customrole1.json` soubor v následujícím příkladu. Id by měla `null` být nastavena na při počátečním vytvoření role jako nové ID je generována automaticky. 

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
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Pokud chcete roli přidat k předplatným, spusťte následující příkaz Prostředí PowerShell:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

Podobně jako při vytváření vlastní role můžete upravit existující `PSRoleDefinition` vlastní roli pomocí objektu nebo šablony JSON.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Aktualizace vlastní role pomocí objektu PSRoleDefinition

Chcete-li upravit vlastní roli, nejprve použijte příkaz [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) k načtení definice role. Za druhé proveďte požadované změny definice role. Nakonec pomocí příkazu [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) uložte upravenou definici role.

Následující příklad přidá `Microsoft.Insights/diagnosticSettings/*` operaci do vlastní role *Operátor virtuálního počítače.*

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

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

Následující příklad přidá předplatné Azure do přiřaditelných oborů vlastní role *Operátor virtuálního počítače.*

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

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

Následující příklad přidá skupinu `AssignableScopes` pro správu vlastní role *Operátor virtuálního počítače.* Přidání skupiny `AssignableScopes` pro správu do aplikace je aktuálně ve verzi Preview.

```azurepowershell
Get-AzManagementGroup

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/{groupId1}")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzManagementGroup

Id          : /providers/Microsoft.Management/managementGroups/marketing-group
Type        : /providers/Microsoft.Management/managementGroups
Name        : marketing-group
TenantId    : 99999999-9999-9999-9999-999999999999
DisplayName : Marketing group

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/marketing-group")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222,
                   /providers/Microsoft.Management/managementGroups/marketing-group}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Aktualizace vlastní role pomocí šablony JSON

Pomocí předchozí šablony JSON můžete snadno upravit existující vlastní roli a přidat nebo odebrat akce. Aktualizujte šablonu JSON a přidejte akci pro čtení pro síť, jak je znázorněno v následujícím příkladu. Definice uvedené v šabloně nejsou kumulativní použity na existující definici, což znamená, že role se zobrazí přesně tak, jak zadáte v šabloně. Je také nutné aktualizovat pole ID id role. Pokud si nejste jisti, co je tato hodnota, můžete použít [Rutina Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) získat tyto informace.

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
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Chcete-li aktualizovat existující roli, spusťte následující příkaz prostředí PowerShell:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Odstranění vlastní role

Chcete-li odstranit vlastní roli, použijte příkaz [Remove-AzRoleDefinition.](/powershell/module/az.resources/remove-azroledefinition)

Následující příklad odebere vlastní roli *Operátor virtuálního počítače.*

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure PowerShellu](tutorial-custom-role-powershell.md)
- [Vlastní role pro prostředky Azure](custom-roles.md)
- [Operace zprostředkovatele prostředků Azure Resource Manager](resource-provider-operations.md)
