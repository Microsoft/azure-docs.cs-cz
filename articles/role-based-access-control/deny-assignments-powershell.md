---
title: Seznam zamítnout přiřazení pro prostředky Azure pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit seznam uživatelů, skupin, instančních objektů a spravovaných identit, které mají odepřený přístup ke konkrétním prostředku akce v konkrétní rozsahy pomocí Azure Powershellu.
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
ms.openlocfilehash: c1ea26fdb4d60262f89ea6ab0f87220a08c01e68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110482"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Seznam zamítnout přiřazení pro prostředky Azure pomocí Azure Powershellu

[Zamítnout přiřazení](deny-assignments.md) zablokuje uživatelům možnost provádět akce konkrétních prostředků Azure i v případě přiřazení role uděluje přístup. Tento článek popisuje, jak zobrazit seznam zamítnout přiřazení pomocí Azure Powershellu.

> [!NOTE]
> Nelze přímo vytvořit vlastní zamítnout přiřazení. Informace o tom, zakáže se vytvoří přiřazení, najdete v části [zamítnout přiřazení](deny-assignments.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete získat informace o přiřazení odepřít, musíte mít:

- `Microsoft.Authorization/denyAssignments/read` oprávnění, která je obsažena ve většině [předdefinované role pro prostředky Azure](built-in-roles.md)
- [PowerShell ve službě Azure Cloud Shell](/azure/cloud-shell/overview) nebo [prostředí Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Seznam zamítnout přiřazení

### <a name="list-all-deny-assignments"></a>Seznam všech zamítnout přiřazení

Chcete-li vypsat všechny Odepřít přiřazení pro aktuální předplatné, použijte [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Seznam zamítnout přiřazení v oboru skupiny prostředků

Chcete-li vypsat všechny Odepřít přiřazení v oboru skupiny prostředků, použijte [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Seznam zamítnout přiřazení v oboru předplatného

Chcete-li vypsat všechny Odepřít přiřazení v oboru předplatného, použijte [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Pokud chcete získat ID předplatného, najdete ho na **předplatná** okna na webu Azure portal nebo můžete použít [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Další postup

- [Vysvětlení zamítnout přiřazení pro prostředky Azure](deny-assignments.md)
- [Seznam zamítnout přiřazení pro prostředky Azure pomocí webu Azure portal](deny-assignments-portal.md)
- [Seznam zamítnout přiřazení pro prostředky Azure pomocí rozhraní REST API](deny-assignments-rest.md)