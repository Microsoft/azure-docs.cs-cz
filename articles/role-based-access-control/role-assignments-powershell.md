---
title: Přidání nebo odebrání přiřazení rolí pomocí RBAC a Azure PowerShellu
description: Zjistěte, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí řízení přístupu na základě rolí Azure (RBAC) a Azure PowerShellu.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 68a73f622dc69b70870ddc1db16edcf406b63800
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283209"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-powershell"></a>Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure PowerShellu

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Tento článek popisuje, jak přiřadit role pomocí Azure PowerShellu.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat nebo odebrat přiřazení rolí, musíte mít:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, například [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) nebo [Vlastník](built-in-roles.md#owner)
- [PowerShell v Azure Cloud Shellu](/azure/cloud-shell/overview) nebo [Azure PowerShellu](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Získání ID objektů

Chcete-li přidat nebo odebrat přiřazení rolí, může být nutné zadat jedinečné ID objektu. ID má formát: `11111111-1111-1111-1111-111111111111`. ID můžete získat pomocí portálu Azure nebo Azure PowerShellu.

### <a name="user"></a>Uživatel

Chcete-li získat ID objektu pro uživatele Azure AD, můžete použít [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Skupina

Chcete-li získat ID objektu pro skupinu Azure AD, můžete použít [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Aplikace

Chcete-li získat ID objektu pro instanční objekt služby Azure AD (identita používaná aplikací), můžete použít [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Pro instanční objekt použijte ID objektu a **ne** ID aplikace.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

V RBAC, chcete-li udělit přístup, přidáte přiřazení role.

### <a name="user-at-a-resource-group-scope"></a>Uživatel v oboru skupiny prostředků

Chcete-li přidat přiřazení role pro uživatele v oboru skupiny prostředků, použijte [příkaz New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>Použití jedinečného ID role

Existuje několik případů, kdy se název role může změnit, například:

- Používáte vlastní roli a rozhodnete se změnit název.
- Používáte roli náhledu, která má **(Náhled)** v názvu. Po vydání role je role přejmenována.

> [!IMPORTANT]
> Verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I v případě, že je role přejmenována, ID role se nezmění. Pokud k vytvoření přiřazení rolí používáte skripty nebo automatizaci, je osvědčeným postupem použít jedinečné ID role namísto názvu role. Proto pokud role je přejmenován, skripty jsou více pravděpodobné, že pracovat.

Chcete-li přidat přiřazení role pomocí jedinečného ID role namísto názvu role, použijte [příkaz New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

Následující příklad přiřadí roli [přispěvatele virtuálního počítače](built-in-roles.md#virtual-machine-contributor) *uživateli společnosti\@alain example.com* v oboru skupiny prostředků *pharma-sales.* Chcete-li získat jedinečné ID role, můžete použít [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) nebo zobrazit [integrované role pro prostředky Azure](built-in-roles.md).

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>Seskupení v oboru prostředků

Chcete-li přidat přiřazení role pro skupinu v oboru prostředků, použijte [příkaz New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Informace o tom, jak získat ID objektu skupiny, naleznete v tématu [Získání ID objektu](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>Aplikace v oboru předplatného

Chcete-li přidat přiřazení role pro aplikaci v oboru předplatného, použijte [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Informace o tom, jak získat ID objektu aplikace, naleznete v tématu [Získání ID objektu](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>Uživatel v oboru skupiny pro správu

Chcete-li přidat přiřazení role pro uživatele v oboru skupiny pro správu, použijte [new-azroleassignment](/powershell/module/az.resources/new-azroleassignment). Chcete-li získat ID skupiny pro správu, najdete ho na okně **Skupiny pro správu** na webu Azure Portal nebo můžete použít [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

Chcete-li odebrat přístup v rbac, odeberte přiřazení role pomocí [příkazu Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

Následující příklad odebere přiřazení role *přispěvatele virtuálního počítače* z *uživatele\@alain example.com* ve skupině prostředků *pharma-sales:*

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

Následující příklad odebere roli <role_name> z object_id> <v oboru předplatného.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

Následující příklad odebere roli> <role_name z <object_id> v oboru skupiny pro správu.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Pokud se zobrazí chybová zpráva "Zadané informace nemapuje na přiřazení role", `-Scope` ujistěte se, že také zadat parametry nebo. `-ResourceGroupName` Další informace najdete [v tématu Poradce při potížích s RBAC pro prostředky Azure](troubleshooting.md#role-assignments-with-unknown-security-principal).

## <a name="next-steps"></a>Další kroky

- [Seznam přiřazení rolí pomocí Azure RBAC a Azure PowerShell](role-assignments-list-powershell.md)
- [Kurz: Udělení skupiny přístupu k prostředkům Azure pomocí RBAC a Azure PowerShellu](tutorial-role-assignments-group-powershell.md)
- [Kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure PowerShellu](tutorial-custom-role-powershell.md)
- [Správa prostředků pomocí Azure PowerShellu](../azure-resource-manager/management/manage-resources-powershell.md)
