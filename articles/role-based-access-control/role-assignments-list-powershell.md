---
title: Seznam přiřazení rolí pomocí Azure RBAC a Azure PowerShell
description: Zjistěte, k jakým prostředkům mají uživatelé, skupiny, instanční objekty nebo spravované identity přístup pomocí řízení přístupu azure založeného na rolích (RBAC) a Azure PowerShellu.
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0ec3153e5b1bfbe04a079d1cfc44e8e8709784d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931156"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-powershell"></a>Seznam přiřazení rolí pomocí Azure RBAC a Azure PowerShell

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Tento článek popisuje, jak vypsat přiřazení rolí pomocí Azure PowerShellu.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> Pokud vaše organizace zadala funkce správy poskytovateli služeb, který používá [azure delegovanou správu prostředků](../lighthouse/concepts/azure-delegated-resource-management.md), přiřazení rolí autorizovaná tímto poskytovatelem služeb se zde nezobrazí.

## <a name="prerequisites"></a>Požadavky

- [PowerShell v Azure Cloud Shellu](/azure/cloud-shell/overview) nebo [Azure PowerShellu](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>Seznam přiřazení rolí pro aktuální předplatné

Nejjednodušší způsob, jak získat seznam všech přiřazení rolí v aktuálním předplatném (včetně zděděných přiřazení rolí z kořenových a skupin pro správu), je použít [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) bez jakýchkoli parametrů.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Výpis přiřazení rolí pro předplatné

Chcete-li vypsat všechna přiřazení rolí v oboru předplatného, použijte [get-azroleassignment](/powershell/module/az.resources/get-azroleassignment). Chcete-li získat ID předplatného, najdete ho na okně **Předplatná** na webu Azure portal nebo můžete použít [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Výpis přiřazení rolí pro uživatele

Chcete-li vypsat všechny role, které jsou přiřazeny určenému uživateli, použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Chcete-li vypsat všechny role, které jsou přiřazeny určenému uživateli, a role, které jsou přiřazeny skupinám, do kterých uživatel patří, použijte [přiřazení Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Výpis přiřazení rolí pro skupinu prostředků

Chcete-li vypsat všechna přiřazení rolí v oboru skupiny prostředků, použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>Seznam přiřazení rolí pro skupinu pro správu

Chcete-li vypsat všechna přiřazení rolí v oboru skupiny pro správu, použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Chcete-li získat ID skupiny pro správu, najdete ho na okně **Skupiny pro správu** na webu Azure Portal nebo můžete použít [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Seznam přiřazení rolí pro klasického správce služeb a spolusprávce

Chcete-li vypsat přiřazení rolí pro klasického správce předplatného a spolusprávce, použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Seznam přiřazení rolí pro spravovanou identitu

1. Získejte ID objektu spravované identity přiřazené nebo uživatelem. 

    Chcete-li získat ID objektu spravované identity přiřazené uživateli, můžete použít [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. Chcete-li uvést přiřazení rolí, použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>Další kroky

- [Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure PowerShellu](role-assignments-powershell.md)
