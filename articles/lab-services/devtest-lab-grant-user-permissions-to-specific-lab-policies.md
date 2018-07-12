---
title: Udělení uživatelských oprávnění na určitém laboratorním zásady | Dokumentace Microsoftu
description: Zjistěte, jak udělit oprávnění uživatele pro zásady konkrétní testovacího prostředí v DevTest Labs podle potřeb jednotlivých uživatelů
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 2b81c23b5cf9ea5d4bfc47d36ae251f762ffad11
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38539686"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Udělení uživatelských oprávnění na určitém laboratorním zásady
## <a name="overview"></a>Přehled
Tento článek ukazuje, jak pomocí prostředí PowerShell k udělení oprávnění uživatele pro zásady konkrétní testovacího prostředí. Tímto způsobem oprávnění se dají aplikovat podle potřeb jednotlivých uživatelů. Můžete například udělit možnost změnit zásady nastavení virtuálního počítače, ale nikoli zásady náklady na konkrétního uživatele.

## <a name="policies-as-resources"></a>Zásady jako prostředky
Jak je popsáno v [řízení přístupu na základě Role v Azure](../role-based-access-control/role-assignments-portal.md) článku, RBAC umožňuje přesnou správu přístupu prostředků Azure. RBAC můžete oddělit úlohy v rámci týmu DevOps a poskytnout pouze takovou úroveň přístupu pro uživatele, kteří potřebují k provádění svých úloh.

Ve službě DevTest Labs, se zásada typ prostředku, který umožňuje akce RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Jednotlivé zásady testovacího prostředí je prostředek v prostředku typu zásad a je možné přiřadit jako obor pro roli RBAC.

Například, pokud chcete udělit oprávnění pro čtení a zápis uživatele **povolené velikosti virtuálních počítačů** zásad, vytvořili byste vlastní roli, která funguje s **Microsoft.DevTestLab/labs/policySets/policies/*** Akce a pak přiřaďte tuto vlastní roli v rámci příslušné uživatele **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Další informace o vlastních rolích v RBAC, najdete v článku [vlastní role řízení přístupu](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Vytvoření testovacího prostředí vlastní role pomocí prostředí PowerShell
Abyste mohli začít, je potřeba v následujícím článku, který vysvětluje, jak nainstalovat a nakonfigurovat rutiny Azure Powershellu: [ https://azure.microsoft.com/blog/azps-1-0-pre ](https://azure.microsoft.com/blog/azps-1-0-pre).

Po nastavení rutin prostředí Azure PowerShell můžete provádět následující úlohy:

* Vypsání všech operací/akcí pro poskytovatele prostředků.
* Seznam akcí v konkrétní roli:
* Vytvoření vlastní role

Následující skript prostředí PowerShell ukazuje příklady, jak k provádění těchto úkolů:

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
Jakmile se vlastní role, které jste definovali, můžete je přiřadit uživatelům. Aby bylo možné přiřadit vlastní roli uživatele, je nutné nejprve získat **ObjectId** představující uživatele. Chcete-li to mohli udělat, použijte **Get-AzureRmADUser** rutiny.

V následujícím příkladu **ObjectId** z *SomeUser* 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 je uživatel.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Jakmile budete mít **ObjectId** pro uživatele a název vlastní roli, můžete přiřadit danou roli uživatele s **New-AzureRmRoleAssignment** rutiny:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

V předchozím příkladu **AllowedVmSizesInLab** zásady použít. Můžete použít některý z následujících zásad:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
Jakmile jste poskytli oprávnění uživatele pro zásady specifické testovací prostředí, zde je několik dalších kroků, které byste měli zvážit:

* [Zabezpečení přístupu k testovacímu prostředí](devtest-lab-add-devtest-user.md)
* [Nastavení zásad testovacího prostředí](devtest-lab-set-lab-policy.md)
* [Vytvoření šablony testovacího prostředí](devtest-lab-create-template.md)
* [Vytvoření vlastních artefaktů pro virtuální počítače](devtest-lab-artifact-author.md)
* [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)

