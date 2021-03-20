---
title: Udělení uživatelských oprávnění ke konkrétním zásadám testovacího prostředí | Microsoft Docs
description: Naučte se, jak udělit uživatelům oprávnění ke konkrétním zásadám testovacího prostředí v DevTest Labs na základě potřeb jednotlivých uživatelů.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 976862476d25e4e9a4933d8a5319eec9d77ca39b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92328466"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Udělení uživatelských oprávnění ke konkrétním zásadám testovacího prostředí
## <a name="overview"></a>Přehled
Tento článek ukazuje, jak pomocí PowerShellu udělit uživatelům oprávnění ke konkrétním zásadám testovacího prostředí. Tímto způsobem lze oprávnění použít na základě potřeb jednotlivých uživatelů. Můžete třeba udělit konkrétnímu uživateli možnost změnit nastavení zásad virtuálního počítače, ale ne zásady pro náklady.

## <a name="policies-as-resources"></a>Zásady jako prostředky
Jak je popsáno v článku [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) , Azure RBAC umožňuje detailní řízení přístupu k prostředkům pro Azure. Pomocí Azure RBAC můžete oddělit povinnosti v rámci týmu DevOps a udělit jenom přístup uživatelům, kteří potřebují k provádění svých úloh.

V DevTest Labs je zásada typem prostředku, který umožňuje akci Azure RBAC **Microsoft. DevTestLab/Labs/policySets/policies**/. Každá zásada testovacího prostředí je prostředek v typu prostředku zásady a dá se přiřadit jako obor pro roli Azure.

Pokud třeba chcete uživatelům udělit oprávnění ke čtení a zápisu pro zásady **povolených velikostí virtuálních počítačů** , vytvoříte vlastní roli, která bude fungovat s **Microsoft. DevTestLab/Labs/policySets/** actions/action, a pak těmto vlastním rolím přiřadíte příslušné uživatele v oboru **Microsoft. DevTestLab/Labs/PolicySets/** Policy/AllowedVmSizesInLab.

Další informace o vlastních rolích ve službě Azure RBAC najdete v tématu [vlastní role Azure](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Vytvoření vlastní role testovacího prostředí pomocí prostředí PowerShell
Aby bylo možné začít, budete muset [nainstalovat Azure PowerShell](/powershell/azure/install-az-ps). 

Po nastavení rutin Azure PowerShell můžete provádět následující úlohy:

* Vypíše všechny operace nebo akce pro poskytovatele prostředků.
* Vypsat akce v určité roli:
* Vytvoření vlastní role

Následující skript prostředí PowerShell ukazuje příklady provedení těchto úloh:

```azurepowershell
# List all the operations/actions for a resource provider.
Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

# List actions in a particular role.
(Get-AzRoleDefinition "DevTest Labs User").Actions

# Create custom role.
$policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
$policyRoleDef.Id = $null
$policyRoleDef.Name = "Policy Contributor"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
$policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)
```

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Přiřazení oprávnění uživateli pro konkrétní zásady pomocí vlastních rolí
Po definování vlastních rolí je můžete přiřadit uživatelům. Chcete-li uživateli přiřadit vlastní roli, musíte nejprve získat **identifikátor objectID** představující tohoto uživatele. K tomu použijte rutinu **Get-AzADUser** .

V následujícím příkladu je **identifikátor objectID** uživatele *SomeUser* 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

```azurepowershell
PS C:\>Get-AzADUser -SearchString "SomeUser"

DisplayName                    Type                           ObjectId
-----------                    ----                           --------
someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3
```

Jakmile máte **identifikátor objectID** pro uživatele a vlastní název role, můžete tuto roli přiřadit uživateli pomocí rutiny **New-AzRoleAssignment** :

```azurepowershell
PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab
```

V předchozím příkladu se používá zásada **AllowedVmSizesInLab** . Můžete použít kteroukoli z následujících zásad:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
Po udělení uživatelských oprávnění ke konkrétním zásadám testovacího prostředí můžete zvážit několik dalších kroků, které je potřeba vzít v úvahu:

* [Zabezpečení přístupu k testovacímu prostředí](devtest-lab-add-devtest-user.md)
* [Nastavení zásad testovacího prostředí](devtest-lab-set-lab-policy.md)
* [Vytvoření šablony testovacího prostředí](devtest-lab-create-template.md)
* [Vytvoření vlastních artefaktů pro virtuální počítače](devtest-lab-artifact-author.md)
* [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)
