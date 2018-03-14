---
title: "Udělení uživatelských oprávnění k zásad konkrétní testovacího | Microsoft Docs"
description: "Postup udělení uživatelských oprávnění k zásady konkrétní testovacího prostředí v DevTest Labs podle potřeb jednotlivých uživatelů"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: v-craic
ms.openlocfilehash: f92ad5e991bdb066bb9680b4865501076d43f450
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Udělení uživatelských oprávnění zásad konkrétní testovacího prostředí
## <a name="overview"></a>Přehled
Tento článek ukazuje, jak pomocí prostředí PowerShell k udělení oprávnění uživatelům zásadu konkrétní prostředí. Toto oprávnění můžete být použito podle potřeb jednotlivých uživatelů. Například můžete chtít udělit možnost měnit nastavení zásad virtuálních počítačů, ale není zásady náklady na konkrétního uživatele.

## <a name="policies-as-resources"></a>Zásady jako prostředky
Jak je popsáno v [řízení přístupu na základě Role v Azure](../active-directory/role-based-access-control-configure.md) článku RBAC umožňuje přesnou správu přístupu prostředků Azure. RBAC můžete oddělit povinností v rámci týmu DevOps a poskytnout pouze takovou úroveň přístupu pro uživatele, kteří potřebují k provádění svých úloh.

V DevTest Labs zásady je typ prostředku, který umožňuje akci RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Každá zásada testovacího prostředí je prostředek v typ prostředku zásad a můžete přiřadit jako obor do RBAC role.

Třeba, aby bylo možné udělit oprávnění pro čtení a zápis uživatele **povolené velikosti virtuálních počítačů** zásady, by vytvořit vlastní roli, která funguje s **Microsoft.DevTestLab/labs/policySets/policies/*** Akce a pak mu přiřaďte příslušné uživatele pro tuto vlastní roli, v rámci oboru **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Další informace o vlastních rolích v RBAC najdete v tématu [vlastní role řízení přístupu](../active-directory/role-based-access-control-custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Vytvoření testovacího prostředí vlastní role pomocí prostředí PowerShell
Chcete-li začít, budete muset v následujícím článku se dozvíte, jak nainstalovat a nakonfigurovat rutin prostředí Azure PowerShell: [ https://azure.microsoft.com/blog/azps-1-0-pre ](https://azure.microsoft.com/blog/azps-1-0-pre).

Jakmile jste nastavili rutin prostředí Azure PowerShell, můžete provádět následující úlohy:

* Seznam všech operací nebo akce pro poskytovatele prostředků
* Seznam akcí v konkrétní roli:
* Vytvořit vlastní roli

Následující skript prostředí PowerShell ukazuje příklady, jak provádět tyto úlohy:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Přiřazení oprávnění pro uživatele pro konkrétní zásady pomocí vlastní role
Jakmile vlastní role, které jste definovali, můžete je přiřadit uživatelům. Chcete-li přiřadit vlastní role pro uživatele, je nutné nejprve získat **ObjectId** představující tohoto uživatele. Chcete-li provést, použijte **Get-AzureRmADUser** rutiny.

V následujícím příkladu **ObjectId** z *SomeUser* 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 je uživatel.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Jakmile máte **ObjectId** pro uživatele a název vlastní roli, lze přiřadit této role uživatele s **New-AzureRmRoleAssignment** rutiny:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

V předchozím příkladu **AllowedVmSizesInLab** zásada se používá. Můžete použít některou z následujících zásad:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
Jednou jste udělena oprávnění uživatele k testovacím konkrétní zásady, tady jsou některé další kroky je potřeba vzít v úvahu:

* [Zabezpečení přístupu k testovacímu prostředí](devtest-lab-add-devtest-user.md)
* [Nastavení zásad testovacího prostředí](devtest-lab-set-lab-policy.md)
* [Vytvoření šablony testovacího prostředí](devtest-lab-create-template.md)
* [Vytvoření vlastních artefaktů pro virtuální počítače](devtest-lab-artifact-author.md)
* [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)

