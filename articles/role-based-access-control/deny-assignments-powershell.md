---
title: Seznam odepřít přiřazení pro prostředky Azure s Azure PowerShell
description: Zjistěte, jak pomocí Azure PowerShellu vypsat uživatele, skupiny, instanční objekty a spravované identity, kterým byl odepřen přístup ke konkrétním akcím prostředků Azure v konkrétních oborech.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5ba18b89bd37dbd55350321c503e37ab0590ab87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137400"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Seznam odepřít přiřazení pro prostředky Azure pomocí Azure PowerShell

[Odepřít přiřazení](deny-assignments.md) blokovat uživatelům provádět konkrétní akce prostředků Azure i v případě, že přiřazení role jim uděluje přístup. Tento článek popisuje, jak vypsat odepřít přiřazení pomocí Azure PowerShell.

> [!NOTE]
> Nelze přímo vytvořit vlastní přiřazení odepřít. Informace o způsobu vytváření přiřazení odepření naleznete v tématu [Odepřít přiřazení](deny-assignments.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li získat informace o odepření přiřazení, musíte mít:

- `Microsoft.Authorization/denyAssignments/read`oprávnění, které je součástí většiny [předdefinovaných rolí pro prostředky Azure](built-in-roles.md)
- [PowerShell v Azure Cloud Shellu](/azure/cloud-shell/overview) nebo [Azure PowerShellu](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Zobrazení seznamu zamítnutých přiřazení

### <a name="list-all-deny-assignments"></a>Vypsat všechna odepření přiřazení

Chcete-li vypsat všechna odepření přiřazení pro aktuální předplatné, použijte [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Zakázat přiřazení seznamu v oboru skupiny zdrojů

Chcete-li vypsat všechna přiřazení odepření v oboru skupiny prostředků, použijte [příkaz Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Seznam odepřít přiřazení v oboru předplatného

Chcete-li vypsat všechna odepření přiřazení v oboru předplatného, použijte [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Chcete-li získat ID předplatného, najdete ho na okně **Předplatná** na webu Azure portal nebo můžete použít [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Další kroky

- [Principy odepření přiřazení pro prostředky Azure](deny-assignments.md)
- [Seznam odepřít přiřazení pro prostředky Azure pomocí portálu Azure](deny-assignments-portal.md)
- [Seznam odepřít přiřazení pro prostředky Azure pomocí rozhraní REST API](deny-assignments-rest.md)