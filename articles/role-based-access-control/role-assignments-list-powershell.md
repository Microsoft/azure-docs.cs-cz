---
title: Vypsání přiřazení rolí Azure pomocí Azure PowerShell – Azure RBAC
description: Naučte se, jak určit, které prostředky uživatelé, skupiny, instanční objekty nebo spravované identity mají přístup k používání Azure PowerShell a řízení přístupu na základě role v Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/28/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: bf5445f6ca04e56aab466e97967a58c3e4b735a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556939"
---
# <a name="list-azure-role-assignments-using-azure-powershell"></a>Vypsání přiřazení rolí Azure pomocí Azure PowerShell

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Tento článek popisuje, jak zobrazit seznam přiřazení rolí pomocí Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> Pokud má vaše organizace samoobslužné funkce správy pro poskytovatele služeb, který používá [správu delegovaných prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md), tady se nezobrazí přiřazení rolí autorizovaných tímto poskytovatelem služeb.

## <a name="prerequisites"></a>Předpoklady

- [PowerShell v Azure Cloud Shell](../cloud-shell/overview.md) nebo [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>Seznam přiřazení rolí pro aktuální předplatné

Nejjednodušší způsob, jak získat seznam všech přiřazení rolí v rámci aktuálního předplatného (včetně děděných přiřazení rolí z kořenu a skupin pro správu), je použít [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) bez parametrů.

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

Pokud chcete zobrazit seznam všech přiřazení rolí v oboru předplatného, použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Pokud chcete získat ID předplatného, najdete ho v okně **předplatná** v Azure Portal nebo můžete použít [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Výpis přiřazení rolí pro uživatele

Chcete-li zobrazit seznam všech rolí, které jsou přiřazeny určitému uživateli, použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Chcete-li zobrazit seznam všech rolí, které jsou přiřazeny určenému uživateli, a rolím, které jsou přiřazeny ke skupinám, do kterých uživatel patří, použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Výpis přiřazení rolí pro skupinu prostředků

Pokud chcete zobrazit seznam všech přiřazení rolí v oboru skupiny prostředků, použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

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

Chcete-li zobrazit seznam všech přiřazení rolí v oboru skupiny pro správu, použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Pokud chcete získat ID skupiny pro správu, můžete ji najít v okně **skupiny pro správu** v Azure Portal nebo můžete použít [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-a-resource"></a>Seznam přiřazení rolí pro určitý prostředek

K vypsání přiřazení rolí pro konkrétní prostředek použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) a `-Scope` parametr. Rozsah se bude lišit v závislosti na prostředku. Chcete-li získat rozsah, můžete spustit `Get-AzRoleAssignment` bez parametrů pro výpis všech přiřazení rolí a potom najít rozsah, který chcete zobrazit.

```azurepowershell
Get-AzRoleAssignment -Scope "/subscriptions/<subscription_id>/resourcegroups/<resource_group_name>/providers/<provider_name>/<resource_type>/<resource>
```

Následující příklad ukazuje, jak zobrazit seznam přiřazení rolí pro účet úložiště. Všimněte si, že tento příkaz obsahuje taky přiřazení rolí ve vyšších oborech, jako jsou skupiny prostředků a předplatná, která se vztahují na tento účet úložiště.

```Example
PS C:\> Get-AzRoleAssignment -Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"
```

Pokud chcete pouze seznam přiřazení rolí, která jsou přiřazena přímo na prostředku, můžete k filtrování seznamu použít příkaz [Where-Object](/powershell/module/microsoft.powershell.core/where-object) .

```Example
PS C:\> Get-AzRoleAssignment | Where-Object {$_.Scope -eq "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"}
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Seznam přiřazení rolí pro klasického správce služeb a spolusprávce

K zobrazení seznamu přiřazení rolí pro klasického Správce předplatného a spolusprávce použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Seznam přiřazení rolí pro spravovanou identitu

1. Získejte ID objektu spravované identity přiřazené systémem nebo uživatelem. 

    Chcete-li získat ID objektu spravované identity přiřazené uživatelem, můžete použít [příkaz Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. K zobrazení seznamu přiřazení rolí použijte [příkaz Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure pomocí Azure PowerShell](role-assignments-powershell.md)