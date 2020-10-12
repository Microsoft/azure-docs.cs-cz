---
title: Přidání nebo odebrání přiřazení rolí Azure pomocí Azure PowerShell – Azure RBAC
description: Naučte se, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí Azure PowerShell a řízení přístupu na základě role v Azure (Azure RBAC).
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9b0df4337a5e5faff3427222fb66caf8e02184a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86146654"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Přidání nebo odebrání přiřazení rolí v Azure pomocí Azure PowerShellu

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Tento článek popisuje, jak přiřadit role pomocí Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat nebo odebrat přiřazení rolí, je nutné mít následující:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)
- [PowerShell v Azure Cloud Shell](/azure/cloud-shell/overview) nebo [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Získat ID objektů

Pokud chcete přidat nebo odebrat přiřazení rolí, možná budete muset zadat jedinečné ID objektu. ID má formát: `11111111-1111-1111-1111-111111111111` . ID můžete získat pomocí Azure Portal nebo Azure PowerShell.

### <a name="user"></a>Uživatel

Pokud chcete získat ID objektu pro uživatele Azure AD, můžete použít [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Group (Skupina)

Pokud chcete získat ID objektu pro skupinu Azure AD, můžete použít [příkaz Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Aplikace

Chcete-li získat ID objektu pro instanční objekt služby Azure AD (identita, kterou používá aplikace), můžete použít [příkaz Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). U instančního objektu použijte ID objektu, **nikoli** ID aplikace.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

Když v Azure RBAC udělíte přístup, přidáte přiřazení role.

### <a name="user-at-a-resource-group-scope"></a>Uživatel v oboru skupiny prostředků

Pokud chcete přidat přiřazení role pro uživatele v oboru skupiny prostředků, použijte [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

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

V některých případech se může stát, že se název role změní například takto:

- Používáte vlastní roli a Vy se rozhodnete název změnit.
- V názvu používáte roli Preview, která má **(Preview)** . Po uvolnění role se role přejmenuje.

> [!IMPORTANT]
> Verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I když je role přejmenována, ID role se nezmění. Pokud k vytváření přiřazení rolí používáte skripty nebo automatizaci, je vhodné místo názvu role použít jedinečné ID role. Proto pokud je role přejmenována, vaše skripty budou pravděpodobnější, že budou fungovat.

K přidání přiřazení role pomocí jedinečného ID role místo názvu role použijte [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -Scope <resource_group_name/resource/management groups>
```

Následující příklad přiřadí roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) k *Alain uživatele \@ example.com* v oboru skupiny prostředků *Pharma-Sales* . Pokud chcete získat jedinečné ID role, můžete použít [příkaz Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) nebo si přečtěte si [předdefinované role Azure](built-in-roles.md).

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

### <a name="group-at-a-resource-scope"></a>Seskupit v oboru prostředku

Pokud chcete přidat přiřazení role pro skupinu v oboru prostředků, použijte [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Informace o tom, jak získat ID objektu skupiny, najdete v tématu [získání ID objektů](#get-object-ids).

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

Pokud chcete přidat přiřazení role pro aplikaci v oboru předplatného, použijte [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Informace o tom, jak získat ID objektu aplikace, najdete v tématu [získání ID objektů](#get-object-ids).

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

Chcete-li přidat přiřazení role pro uživatele v oboru skupiny pro správu, použijte [příkaz New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Pokud chcete získat ID skupiny pro správu, můžete ji najít v okně **skupiny pro správu** v Azure Portal nebo můžete použít [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

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

Pokud chcete v Azure RBAC odebrat přístup, odeberte přiřazení role pomocí funkce [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

Následující příklad odebere přiřazení role *Přispěvatel virtuálních počítačů* z uživatele *Alain \@ example.com* ve skupině prostředků *Pharma-Sales* :

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

Následující příklad odebere <role_name> role ze <object_id> v oboru předplatného.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

Následující příklad odebere <role_name> role ze <object_id> v oboru skupiny pro správu.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Pokud se zobrazí chybová zpráva: "zadané informace nejsou namapovány na přiřazení role", ujistěte se, že jste zadali také `-Scope` `-ResourceGroupName` parametry nebo. Další informace najdete v tématu [řešení potíží s Azure RBAC](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="next-steps"></a>Další kroky

- [Vypsání přiřazení rolí Azure pomocí Azure PowerShell](role-assignments-list-powershell.md)
- [Kurz: udělení přístupu skupině k prostředkům Azure pomocí Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Kurz: Vytvoření vlastní role Azure pomocí Azure PowerShell](tutorial-custom-role-powershell.md)
- [Správa prostředků pomocí Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
