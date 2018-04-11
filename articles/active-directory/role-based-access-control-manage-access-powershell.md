---
title: "Správa řízení přístupu na základě rolí (RBAC) pomocí prostředí Azure PowerShell | Microsoft Docs"
description: "Jak spravovat RBAC pomocí Azure Powershellu, včetně obsahující seznam rolí, přiřazení rolí a odstraňovat přiřazení role."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 5e7e01502a173cb93216c77dd43f61094fd88148
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Správa řízení přístupu podle role pomocí prostředí Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)

S na základě rolí řízení přístupu (RBAC), můžete definovat přístupu pro uživatele, skupiny a objekty služby podle přiřazení rolí v určitém rozsahu. Tento článek popisuje, jak můžete spravovat přístup pomocí prostředí Azure PowerShell.

Před prostředí PowerShell můžete použít ke správě RBAC, je třeba následující požadavky:

* Azure PowerShell verze 0.8.8 nebo novější. Nainstalujte nejnovější verzi a přidružit ho ke svému předplatnému Azure, najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
* Rutiny Azure Resource Manager. Nainstalujte [rutiny Azure Resource Manager](/powershell/azure/overview) v prostředí PowerShell.

## <a name="list-roles"></a>Seznam rolí
### <a name="list-all-available-roles"></a>Zobrazí seznam všech dostupných rolí
Rolím RBAC seznamu, které jsou k dispozici pro přiřazení a chcete-li prověřit operací, u kterých se udělit přístup, použijte `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC prostředí PowerShell-Get AzureRmRoleDefinition – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Seznam akcí role
K zobrazení seznamu akce pro určité role, použijte `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC prostředí PowerShell-Get AzureRmRoleDefinition pro určité role – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Zjistit, kdo má přístup
Přiřazení přístupu RBAC seznamu, použijte `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Seznam přiřazení rolí na konkrétní rozsah
Můžete zobrazit všechna přiřazení přístupu pro zadané předplatné, skupinu prostředků nebo prostředek. Například pokud chcete zobrazit všech active přiřazení pro skupinu prostředků, použijte `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC prostředí PowerShell - Get-AzureRmRoleAssignment pro skupinu prostředků – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>Seznam rolí přiřazen k uživateli
K zobrazení seznamu všech rolí, které jsou přiřazeny pro zadaného uživatele a role, které jsou přiřazeny do skupin, do kterých uživatel patří, použijte `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC prostředí PowerShell - Get-AzureRmRoleAssignment pro uživatele – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Seznam přiřazení rolí správce a coadmin classic služby
K vypsání přiřazení přístupu pro správce classic předplatného a coadministrators, použijte:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Udělení přístupu
### <a name="search-for-object-ids"></a>Vyhledejte ID objektů
Pokud chcete přiřadit roli, musíte určit objektu (uživatele, skupiny nebo aplikace) a obor.

Pokud si nejste jisti ID předplatného, najdete ji v **odběry** okno na portálu Azure. Další postup dotazování pro ID předplatného najdete v tématu [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) na webu MSDN.

ID objektu pro skupinu služby Azure AD, použijte:

    Get-AzureRmADGroup -SearchString <group name in quotes>

ID objektu pro objekt zabezpečení služby Azure AD nebo aplikaci, použijte:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Přiřazení role k aplikaci v oboru předplatného
Chcete-li udělit přístup k aplikaci v oboru předplatné, použijte:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC prostředí PowerShell - nové-AzureRmRoleAssignment – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Přiřazení role uživatele v oboru skupiny prostředků
Pokud chcete udělit přístup na uživatele v oboru skupiny prostředků, použijte:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC prostředí PowerShell - nové-AzureRmRoleAssignment – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Přiřazení role do skupiny v oboru prostředků
Pokud chcete udělit přístup do skupiny v oboru prostředků, použijte:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC prostředí PowerShell - nové-AzureRmRoleAssignment – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Odebrání přístupu
Pokud chcete odebrat přístup pro uživatele, skupiny a aplikace, použijte:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC prostředí PowerShell - Remove-AzureRmRoleAssignment – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Vytvořit vlastní roli
Chcete-li vytvořit vlastní roli, použijte ```New-AzureRmRoleDefinition``` příkaz. Strukturování roli pomocí PSRoleDefinitionObject nebo šablonu JSON dvěma způsoby. 

## <a name="get-actions-for-a-resource-provider"></a>Získat akce pro poskytovatele prostředků
Při vytváření vlastní role od začátku, je důležité vědět, všechny možné operace od poskytovatelů prostředků.
Použití ```Get-AzureRMProviderOperation``` získat tyto informace.
Například pokud chcete zkontrolovat všechny operace, které jsou k dispozici pro virtuální počítač použijte tento příkaz:

```
Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation , Description -AutoSize
```

### <a name="create-role-with-psroledefinitionobject"></a>Vytvořit roli s PSRoleDefinitionObject
Pokud používáte PowerShell můžete vytvořit vlastní roli, můžete začít úplně od začátku nebo použijte jednu z [předdefinované role](role-based-access-built-in-roles.md) jako výchozí bod. Příklad v této části začíná předdefinovaná role a pak ho přizpůsobí s další oprávnění. Upravit atributy, které se přidat *akce*, *notActions*, nebo *obory* a potom uložte změny jako novou roli.

V následujícím příkladu začíná *Přispěvatel virtuálních počítačů* role a používá, chcete-li vytvořit vlastní roli volají *operátor virtuální počítač*. Nová role uděluje přístup ke všem operacím čtení z *Microsoft.Compute*, *Microsoft.Storage*, a *Microsoft.Network* zprostředkovatelé a uděluje přístup k prostředkům na spuštění, restartování a monitorování virtuálních počítačů. Vlastní role lze použít ve dvou odběry.

```
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
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![RBAC prostředí PowerShell-Get AzureRmRoleDefinition – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

### <a name="create-role-with-json-template"></a>Vytvoření role pomocí šablony JSON
Šablonu JSON slouží jako zdroj definice pro vlastní roli. Následující příklad vytvoří vlastní role, která umožňuje přístup pro čtení na úložiště a výpočetní prostředky, přístup k podporovat, a přidá do dvou odběry této role. Vytvoření nového souboru `C:\CustomRoles\customrole1.json` s následující příklad. Id musí být nastavena na `null` na vytvoření počáteční role jako nové ID je generován automaticky. 

```
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
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```
Přidejte roli k předplatným, spusťte následující příkaz prostředí PowerShell:
```
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>Upravit vlastní roli
Podobně jako u vytváření vlastní roli, můžete upravit existující vlastní role pomocí PSRoleDefinitionObject nebo šablonu JSON.

### <a name="modify-role-with-psroledefinitionobject"></a>Upravit role s PSRoleDefinitionObject
Chcete-li upravit vlastní roli, nejprve použijte `Get-AzureRmRoleDefinition` příkaz načíst definici role. Za druhé proveďte požadované změny k definici role. Nakonec použijte `Set-AzureRmRoleDefinition` příkaz pro uložení definice upravené role.

Následující příklad přidá `Microsoft.Insights/diagnosticSettings/*` operace *virtuální počítač operátor* vlastní role.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC prostředí PowerShell - Set-AzureRmRoleDefinition – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

Následující příklad přidá do přiřaditelnými obory z předplatného Azure *virtuální počítač operátor* vlastní role.

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC prostředí PowerShell - Set-AzureRmRoleDefinition – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

### <a name="modify-role-with-json-template"></a>Upravit role pomocí šablony JSON
Pomocí předchozího šablony JSON, můžete snadno upravit existující vlastní roli přidat nebo odebrat akce. Aktualizovat šablonu JSON a přidejte čtení akce pro sítě, jak je znázorněno v následujícím příkladu. Definice uvedené v šabloně nepoužívají kumulativně existující definice, což znamená, že role zobrazí přesně tak, jak zadáte v šabloně. Také musíte aktualizovat pole Id s ID role. Pokud si nejste jisti, co je tato hodnota, můžete použít `Get-AzureRmRoleDefinition` rutiny získat tyto informace.

```
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
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

Pokud chcete aktualizovat existující roli, spusťte následující příkaz prostředí PowerShell:
```
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Odstranit vlastní roli
Chcete-li odstranit vlastní roli, použijte `Remove-AzureRmRoleDefinition` příkaz.

Následující příklad odebere *virtuální počítač operátor* vlastní role.

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC prostředí PowerShell - Remove-AzureRmRoleDefinition – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Vlastní role seznamu
K zobrazení seznamu rolí, které jsou k dispozici pro přiřazení v oboru, použijte `Get-AzureRmRoleDefinition` příkaz.

Následující příklad vypíše všechny role, které jsou k dispozici pro přiřazení ve vybraném předplatném.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC prostředí PowerShell-Get AzureRmRoleDefinition – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

V následujícím příkladu *operátor virtuálního počítače* vlastní role není k dispozici v *Production4* předplatné vzhledem k tomu, že toto předplatné není v **AssignableScopes** role.

![RBAC prostředí PowerShell-Get AzureRmRoleDefinition – snímek obrazovky](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>Další informace najdete v tématech
* [Použití Azure PowerShell s Azure Resource Manager](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

