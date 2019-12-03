---
title: Vytvoření nebo aktualizace vlastních rolí pro prostředky Azure pomocí Azure PowerShell | Microsoft Docs
description: Naučte se, jak vypisovat, vytvářet, aktualizovat nebo odstraňovat vlastní role pomocí řízení přístupu na základě role (RBAC) pro prostředky Azure pomocí Azure PowerShell.
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
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: fa4ff5f35df0f541d8a7e633df024af81676e58b
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74703095"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-powershell"></a>Vytvoření nebo aktualizace vlastních rolí pro prostředky Azure pomocí Azure PowerShell

Pokud [předdefinované role pro prostředky Azure](built-in-roles.md) nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní role. Tento článek popisuje, jak pomocí Azure PowerShell vypisovat, vytvářet, aktualizovat nebo odstraňovat vlastní role.

Podrobný návod, jak vytvořit vlastní roli, najdete v tématu [kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure PowerShell](tutorial-custom-role-powershell.md).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

K vytvoření vlastních rolí budete potřebovat:

- Oprávnění k vytváření vlastních rolí, například [Vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) nebo [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Výpis vlastních rolí

K zobrazení seznamu rolí, které jsou k dispozici pro přiřazení v oboru, použijte příkaz [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) . Následující příklad vypíše všechny role, které jsou k dispozici pro přiřazení ve vybraném předplatném.

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

Následující příklad vypíše pouze vlastní role, které jsou k dispozici pro přiřazení ve vybraném předplatném.

```azurepowershell
Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Pokud vybrané předplatné není v `AssignableScopes` role, nebude tato vlastní role uvedená.

## <a name="list-a-custom-role-definition"></a>Seznam definice vlastní role

K vypsání vlastní definice role použijte [příkaz Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Jedná se o stejný příkaz, jaký používáte pro předdefinovanou roli.

```azurepowershell
Get-AzRoleDefinition <role name> | ConvertTo-Json
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

Následující příklad zobrazí seznam pouze akcí role:

```azurepowershell
(Get-AzRoleDefinition <role name>).Actions
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

K vytvoření vlastní role použijte příkaz [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) . Existují dvě metody uspořádání role pomocí `PSRoleDefinition`ho objektu nebo šablony JSON. 

### <a name="get-operations-for-a-resource-provider"></a>Získat operace pro poskytovatele prostředků

Když vytváříte vlastní role, je důležité znát všechny možné operace od poskytovatelů prostředků.
Můžete zobrazit seznam [operací poskytovatele prostředků](resource-provider-operations.md) , nebo můžete k získání těchto informací použít příkaz [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) .
Pokud například chcete ověřit všechny dostupné operace pro virtuální počítače, použijte tento příkaz:

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

Když použijete PowerShell k vytvoření vlastní role, můžete jako výchozí bod použít jednu z [předdefinovaných rolí](built-in-roles.md) nebo můžete začít od začátku. První příklad v této části začíná integrovanou rolí a pak ji přizpůsobuje s více oprávněními. Upravte atributy pro přidání `Actions`, `NotActions`nebo `AssignableScopes`, které chcete, a pak změny uložte jako novou roli.

V následujícím příkladu se spustí předdefinovaná role [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) , aby se vytvořila vlastní role s názvem *operátor virtuálního počítače*. Nová role uděluje přístup ke všem operacím čtení poskytovatele prostředků *Microsoft. COMPUTE*, *Microsoft. Storage*a *Microsoft. Network* a uděluje přístup ke spouštění, restartování a monitorování virtuálních počítačů. Vlastní roli lze použít ve dvou předplatných.

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

Následující příklad ukazuje jiný způsob vytvoření vlastní role *operátoru virtuálního počítače* . Spustí se vytvořením nového objektu `PSRoleDefinition`. Operace akcí jsou zadány v proměnné `perms` a nastaveny na vlastnost `Actions`. Vlastnost `NotActions` je nastavena čtením `NotActions` z předdefinované role [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) . Vzhledem k tomu, že [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) nemá žádné `NotActions`, tento řádek není povinný, ale zobrazuje, jak lze informace získat z jiné role.

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

### <a name="create-a-custom-role-with-json-template"></a>Vytvoření vlastní role se šablonou JSON

Šablonu JSON lze použít jako definici zdroje pro vlastní roli. Následující příklad vytvoří vlastní roli, která umožňuje oprávnění ke čtení pro úložiště a výpočetní prostředky, přístup k podpoře a přidání této role do dvou předplatných. Vytvořte nový soubor `C:\CustomRoles\customrole1.json` s následujícím příkladem. ID by mělo být nastavené na `null` při vytváření počátečních rolí, protože nové ID se generuje automaticky. 

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

Chcete-li přidat roli k předplatným, spusťte následující příkaz prostředí PowerShell:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

Podobně jako při vytváření vlastní role můžete upravit stávající vlastní roli pomocí objektu `PSRoleDefinition` nebo šablony JSON.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Aktualizace vlastní role pomocí objektu PSRoleDefinition

K načtení vlastní role nejdřív použijte příkaz [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) , aby se načetla definice role. Za druhé proveďte požadované změny definice role. Nakonec pomocí příkazu [set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) uložte upravenou definici role.

Následující příklad přidá operaci `Microsoft.Insights/diagnosticSettings/*` do vlastní role *operátora virtuálního počítače* .

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

Následující příklad přidá předplatné Azure do oborů přiřazení vlastní role *operátoru virtuálního počítače* .

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

### <a name="update-a-custom-role-with-a-json-template"></a>Aktualizace vlastní role pomocí šablony JSON

Pomocí předchozí šablony JSON můžete snadno upravit stávající vlastní roli a přidat nebo odebrat akce. Aktualizujte šablonu JSON a přidejte akci čtení pro síťové služby, jak je znázorněno v následujícím příkladu. Definice uvedené v šabloně se kumulativně nepoužijí pro existující definici, což znamená, že se role zobrazuje přesně tak, jak zadáte v šabloně. Také je potřeba aktualizovat pole ID IDENTIFIKÁTORem role. Pokud si nejste jistí, jakou je tato hodnota, můžete získat tyto informace pomocí rutiny [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) .

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

Pokud chcete odstranit vlastní roli, použijte příkaz [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) .

Následující příklad odebere vlastní roli *operátoru virtuálního počítače* .

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

- [Kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure PowerShell](tutorial-custom-role-powershell.md)
- [Vlastní role pro prostředky Azure](custom-roles.md)
- [Azure Resource Manager operace poskytovatele prostředků](resource-provider-operations.md)
