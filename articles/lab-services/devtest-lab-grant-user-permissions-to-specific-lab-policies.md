---
title: Udělení uživatelských oprávnění určitým zásadám testovacího prostředí | Dokumenty společnosti Microsoft
description: Zjistěte, jak udělit uživatelská oprávnění ke konkrétním zásadám testovacího prostředí v devtest labs na základě potřeb jednotlivých uživatelů
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284210"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Udělení uživatelských oprávnění konkrétním zásadám testovacího prostředí
## <a name="overview"></a>Přehled
Tento článek ukazuje, jak pomocí prostředí PowerShell udělit uživatelům oprávnění k určité zásady testovacího prostředí. Tímto způsobem lze použít oprávnění na základě potřeb každého uživatele. Můžete například udělit konkrétnímu uživateli možnost změnit nastavení zásad virtuálního počítače, ale ne zásady nákladů.

## <a name="policies-as-resources"></a>Zásady jako zdroje
Jak je popsáno v článku [řízení přístupu na základě rolí Azure,](../role-based-access-control/role-assignments-portal.md) RBAC umožňuje jemně odstupňovanou správu přístupu prostředků pro Azure. Pomocí RBAC můžete oddělit povinnosti v rámci týmu DevOps a udělit pouze množství přístupu k uživatelům, které potřebují k provádění svých úloh.

V DevTest Labs je zásada typ prostředku, který umožňuje akci RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Každá zásada testovacího prostředí je zdrojem v typu prostředku zásad a lze ji přiřadit jako obor roli RBAC.

Chcete-li například udělit uživatelům oprávnění ke čtení a zápisu k **zásadám Povolené velikosti virtuálních** počítačů, vytvořte vlastní roli, která bude fungovat s akcí **Microsoft.DevTestLab/labs/policySets/policies/action,** a pak přiřadit příslušné uživatele k této vlastní roli v oboru **Microsoft.DevTest/labs/policySets/policies/AllowedVmSizesInLab**.

Další informace o vlastních rolích v RBAC najdete v tématu [Vlastní role řízení přístupu](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Vytvoření vlastní role testovacího prostředí pomocí PowerShellu
Abyste mohli začít, budete muset [nainstalovat Azure PowerShell](/powershell/azure/install-az-ps). 

Po nastavení rutin prostředí Azure PowerShell můžete provádět následující úkoly:

* Vypsat všechny operace/akce pro poskytovatele prostředků
* Seznam akcí v určité roli:
* Vytvoření vlastní role

Následující skript prostředí PowerShell ilustruje příklady provádění těchto úloh:

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

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Přiřazení oprávnění uživateli pro určitou zásadu pomocí vlastních rolí
Po definování vlastních rolí je můžete přiřadit uživatelům. Chcete-li přiřadit vlastní roli uživateli, musíte nejprve získat **Objekt ObjectId** představující tohoto uživatele. Chcete-li to provést, použijte rutinu **Get-AzADUser.**

V následujícím příkladu **objectid** *someuser* uživatele je 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Jakmile budete mít **ObjectId** pro uživatele a vlastní název role, můžete přiřadit tuto roli uživateli s **rutinou New-AzRoleAssignment:**

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

V předchozím příkladu je použita zásada **AllowedVmSizesInLab.** Můžete použít některou z následujících posla:

* Uživatel MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
Jakmile udělíte uživatelská oprávnění konkrétním zásadám testovacího prostředí, zvažte následující kroky:

* [Zabezpečení přístupu k testovacímu prostředí](devtest-lab-add-devtest-user.md)
* [Nastavení zásad testovacího prostředí](devtest-lab-set-lab-policy.md)
* [Vytvoření šablony testovacího prostředí](devtest-lab-create-template.md)
* [Vytvoření vlastních artefaktů pro virtuální počítače](devtest-lab-artifact-author.md)
* [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)

