---
title: Udělení uživatelských oprávnění ke konkrétním zásadám testovacího prostředí | Microsoft Docs
description: Naučte se, jak udělit uživatelům oprávnění ke konkrétním zásadám testovacího prostředí v DevTest Labs na základě potřeb jednotlivých uživatelů.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: spelluru
ms.openlocfilehash: 9b31f3e68fbabc32f301fdcd8066a3bfbf1c2dbd
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028435"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Udělení uživatelských oprávnění ke konkrétním zásadám testovacího prostředí
## <a name="overview"></a>Přehled
Tento článek ukazuje, jak pomocí PowerShellu udělit uživatelům oprávnění ke konkrétním zásadám testovacího prostředí. Tímto způsobem lze oprávnění použít na základě potřeb jednotlivých uživatelů. Můžete třeba udělit konkrétnímu uživateli možnost změnit nastavení zásad virtuálního počítače, ale ne zásady pro náklady.

## <a name="policies-as-resources"></a>Zásady jako prostředky
Jak je popsáno v článku [Access Control založeném na rolích Azure](../role-based-access-control/role-assignments-portal.md) , umožňuje RBAC přístup k prostředkům pro Azure v jemně odstupňované správě. Pomocí RBAC můžete oddělit povinnosti v rámci týmu DevOps a udělit jenom přístup uživatelům, kteří potřebují k provádění svých úloh.

V DevTest Labs je zásada typem prostředku, který umožňuje akci RBAC **Microsoft. DevTestLab/Labs/policySets/policies**/. Každá zásada testovacího prostředí je prostředek v typu prostředku zásady a dá se přiřadit jako obor role RBAC.

Pokud třeba chcete uživatelům udělit oprávnění ke čtení a zápisu pro zásady **povolených velikostí virtuálních počítačů** , vytvoříte vlastní roli, která bude fungovat s **Microsoft. DevTestLab/Labs/policySets/** actions/action a pak jim přiřadí příslušné uživatele. Tato vlastní role v oboru **Microsoft. DevTestLab/Labs/policySets/policies/AllowedVmSizesInLab**.

Další informace o vlastních rolích v RBAC najdete v tématu [řízení přístupu vlastních rolí](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Vytvoření vlastní role testovacího prostředí pomocí prostředí PowerShell
Aby bylo možné začít, budete muset [nainstalovat Azure PowerShell](/powershell/azure/install-az-ps). 

Po nastavení rutin Azure PowerShell můžete provádět následující úlohy:

* Vypíše všechny operace nebo akce pro poskytovatele prostředků.
* Vypsat akce v určité roli:
* Vytvoření vlastní role

Následující skript prostředí PowerShell ukazuje příklady provedení těchto úloh:

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

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Přiřazení oprávnění uživateli pro konkrétní zásady pomocí vlastních rolí
Po definování vlastních rolí je můžete přiřadit uživatelům. Chcete-li uživateli přiřadit vlastní roli, musíte nejprve získat **identifikátor objectID** představující tohoto uživatele. K tomu použijte rutinu **Get-AzADUser** .

V následujícím příkladu je **identifikátor objectID** uživatele *SomeUser* 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Jakmile máte **identifikátor objectID** pro uživatele a vlastní název role, můžete tuto roli přiřadit uživateli pomocí rutiny **New-AzRoleAssignment** :

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

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

