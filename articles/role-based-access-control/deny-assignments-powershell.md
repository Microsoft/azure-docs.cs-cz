---
title: Vypíše přiřazení zamítnutí pro prostředky Azure s Azure PowerShell.
description: Naučte se, jak zobrazit seznam uživatelů, skupin, instančních objektů a spravovaných identit, kterým byl odepřen přístup ke konkrétním akcím prostředků Azure v určitých oborech pomocí Azure PowerShell.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77137400"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Vypsat přiřazení zamítnutí pro prostředky Azure pomocí Azure PowerShell

[Odmítnutí přiřazení](deny-assignments.md) zablokuje uživatelům, aby prováděli konkrétní akce prostředku Azure i v případě, že jim přiřazením role udělí přístup. Tento článek popisuje, jak vypsat přiřazení zamítnutí pomocí Azure PowerShell.

> [!NOTE]
> Nemůžete přímo vytvořit vlastní přiřazení zamítnutí. Informace o tom, jak se vytvářejí přiřazení odepřít, najdete v tématu [zamítnutí](deny-assignments.md)přiřazení.

## <a name="prerequisites"></a>Požadavky

Chcete-li získat informace o přiřazení zamítnutí, je nutné mít následující:

- `Microsoft.Authorization/denyAssignments/read`oprávnění, které je součástí většiny [předdefinovaných rolí pro prostředky Azure](built-in-roles.md)
- [PowerShell v Azure Cloud Shell](/azure/cloud-shell/overview) nebo [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Zobrazení seznamu zamítnutých přiřazení

### <a name="list-all-deny-assignments"></a>Vypsat všechna přiřazení odepřít

Pokud chcete zobrazit seznam všech přiřazení Odepřít pro aktuální předplatné, použijte [příkaz Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Vypsat přiřazení zamítnutí v oboru skupiny prostředků

Pokud chcete zobrazit seznam všech přiřazení odepřít v oboru skupiny prostředků, použijte [příkaz Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Vypsat přiřazení zamítnutí v oboru předplatného

Pokud chcete zobrazit seznam všech přiřazení odepřít v oboru předplatného, použijte [příkaz Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Pokud chcete získat ID předplatného, najdete ho v okně **předplatná** v Azure Portal nebo můžete použít [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Další kroky

- [Pochopení přiřazení zamítnutí pro prostředky Azure](deny-assignments.md)
- [Vypsat přiřazení zamítnutí pro prostředky Azure pomocí Azure Portal](deny-assignments-portal.md)
- [Vypsat přiřazení zamítnutí pro prostředky Azure pomocí REST API](deny-assignments-rest.md)