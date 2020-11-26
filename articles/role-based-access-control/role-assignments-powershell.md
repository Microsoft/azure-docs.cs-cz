---
title: Přidání nebo odebrání přiřazení rolí Azure pomocí Azure PowerShell – Azure RBAC
description: Naučte se, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí Azure PowerShell a řízení přístupu na základě role v Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2020
ms.author: rolyon
ms.openlocfilehash: c4082f7fc535807ec996034ba695549a51969a99
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182406"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Přidání nebo odebrání přiřazení rolí v Azure pomocí Azure PowerShellu

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Tento článek popisuje, jak přiřadit role pomocí Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

Chcete-li přidat nebo odebrat přiřazení rolí, je nutné mít následující:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)
- [PowerShell v Azure Cloud Shell](../cloud-shell/overview.md) nebo [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="steps-to-add-a-role-assignment"></a>Kroky pro přidání přiřazení role

Když v Azure RBAC udělíte přístup, přidáte přiřazení role. Přiřazení role se skládá ze tří prvků: objekt zabezpečení, definice role a obor. Chcete-li přidat přiřazení role, postupujte podle těchto kroků.

### <a name="step-1-determine-who-needs-access"></a>Krok 1: určení toho, kdo potřebuje přístup

Roli můžete přiřadit uživateli, skupině, objektu služby nebo spravované identitě. Chcete-li přidat přiřazení role, bude pravděpodobně nutné zadat jedinečné ID objektu. ID má formát: `11111111-1111-1111-1111-111111111111` . ID můžete získat pomocí Azure Portal nebo Azure PowerShell.

**Uživatel**

Pro uživatele Azure AD Získejte hlavní název uživatele, například *patlong \@ contoso.com* nebo ID objektu uživatele. Chcete-li získat ID objektu, můžete použít [příkaz Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**Skupina**

Pro skupinu Azure AD potřebujete ID objektu skupiny. Chcete-li získat ID objektu, můžete použít [příkaz Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**Instanční objekt**

Pro instanční objekt služby Azure AD (identita používaný aplikací) potřebujete ID instančního objektu. Chcete-li získat ID objektu, můžete použít [příkaz Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). U instančního objektu použijte ID objektu, **nikoli** ID aplikace.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**Spravovaná identita**

Pro spravovanou identitu přiřazenou systémem nebo uživatelem budete potřebovat ID objektu. Chcete-li získat ID objektu, můžete použít [příkaz Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-find-the-appropriate-role"></a>Krok 2: vyhledání příslušné role

Oprávnění se seskupují do rolí. Můžete vybrat ze seznamu několika [předdefinovaných rolí Azure](built-in-roles.md) nebo můžete použít vlastní role. Osvědčeným postupem je udělit přístup s nejmenším možným oprávněním, takže se nepřiřazují širší role.

Chcete-li zobrazit seznam rolí a získat jedinečné ID role, můžete použít [příkaz Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

Tady je postup, jak zobrazit podrobnosti konkrétní role.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

Další informace najdete v tématu [seznam definic rolí Azure](role-definitions-list.md#azure-powershell).
 
### <a name="step-3-identify-the-needed-scope"></a>Krok 3: určení potřebného oboru

Azure poskytuje čtyři úrovně rozsahu: prostředek, [Skupina prostředků](../azure-resource-manager/management/overview.md#resource-groups), předplatné a [skupina pro správu](../governance/management-groups/overview.md). Osvědčeným postupem je udělit přístup s nejmenším možným oprávněním, takže se nepřiřazujte role v širším rozsahu. Další informace o rozsahu najdete v tématu [pochopení oboru](scope-overview.md).

**Obor prostředku**

V případě oboru prostředků potřebujete ID prostředku pro daný prostředek. ID prostředku najdete tak, že si prohlížíte vlastnosti prostředku ve Azure Portal. ID prostředku má následující formát.

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**Rozsah skupiny prostředků**

Pro rozsah skupiny prostředků potřebujete název skupiny prostředků. Název můžete najít na stránce **skupiny prostředků** v Azure Portal nebo můžete použít [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
Get-AzResourceGroup
```

**Rozsah předplatného** 

Pro rozsah předplatného potřebujete ID předplatného. ID můžete najít na stránce **předplatná** v Azure Portal nebo můžete použít [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```azurepowershell
Get-AzSubscription
```

**Rozsah skupiny pro správu** 

Pro rozsah skupiny pro správu potřebujete název skupiny pro správu. Název můžete najít na stránce **skupiny pro správu** v Azure Portal nebo můžete použít [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-add-role-assignment"></a>Krok 4: přidání přiřazení role

K přidání přiřazení role použijte příkaz [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) . V závislosti na rozsahu má příkaz obvykle jeden z následujících formátů.

**Obor prostředku**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**Rozsah skupiny prostředků**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**Rozsah předplatného** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**Rozsah skupiny pro správu** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="add-role-assignment-examples"></a>Přidat příklady přiřazení rolí

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Přidání přiřazení role pro všechny kontejnery objektů BLOB v oboru prostředků účtu úložiště

Přiřadí roli [Přispěvatel dat objektu BLOB služby Storage](built-in-roles.md#storage-blob-data-contributor) k instančnímu objektu s ID *55555555-5555-5555-5555-555555555555* v oboru prostředků pro účet úložiště s názvem *storage12345*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>Přidání přiřazení role pro konkrétní obor prostředku kontejneru objektů BLOB

Přiřadí roli [Přispěvatel dat objektu BLOB služby Storage](built-in-roles.md#storage-blob-data-contributor) k instančnímu objektu s ID *55555555-5555-5555-5555-555555555555* v oboru prostředků pro kontejner objektů BLOB s názvem *BLOB-Container-01*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Přidání přiřazení role pro skupinu v konkrétním oboru prostředků virtuální sítě

Přiřadí roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) ke skupině *Pharma Sales Admins* s ID aaaaaaaa-AAAA-AAAA-AAAA-aaaaaaaaaaaa v oboru prostředků pro virtuální síť s názvem *Pharma-Sales-Project-Network*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="add-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Přidání přiřazení role pro uživatele v oboru skupiny prostředků

Přiřadí roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) , aby *patlong uživatele \@ contoso.com* v oboru skupiny prostředků *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

Alternativně můžete zadat plně kvalifikovanou skupinu prostředků s `-Scope` parametrem:

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Přidání přiřazení role pro uživatele pomocí jedinečného ID role v oboru skupiny prostředků

V některých případech se může stát, že se název role změní například takto:

- Používáte vlastní roli a Vy se rozhodnete název změnit.
- V názvu používáte roli Preview, která má **(Preview)** . Po uvolnění role se role přejmenuje.

I když je role přejmenována, ID role se nezmění. Pokud k vytváření přiřazení rolí používáte skripty nebo automatizaci, je vhodné místo názvu role použít jedinečné ID role. Proto pokud je role přejmenována, vaše skripty budou pravděpodobnější, že budou fungovat.

Následující příklad přiřadí roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) k uživateli *patlong \@ contoso.com* v oboru skupiny prostředků *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>Přidání přiřazení role pro aplikaci do oboru skupiny prostředků

Přiřadí roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) k aplikaci s ID objektu zabezpečení služby 77777777-7777-7777-7777-777777777777 v oboru skupiny prostředků *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>Přidání přiřazení role pro uživatele v oboru předplatného

Přiřadí roli [Čtenář](built-in-roles.md#reader) k uživateli *annm \@ example.com* v oboru předplatného.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>Přidání přiřazení role pro uživatele v oboru skupiny pro správu

Přiřadí roli [Čtenář fakturace](built-in-roles.md#billing-reader) pro uživatele *Alain \@ example.com* v oboru skupiny pro správu.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

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

Následující příklad odebere přiřazení role [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) z uživatele *patlong \@ contoso.com* ve skupině prostředků *Pharma-Sales* :

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Odebere roli [Čtenář](built-in-roles.md#reader) z týmové skupiny *Ann Mack* s ID 22222222-2222-2222-2222-222222222222 v oboru předplatného.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Odebere roli [Čtenář fakturace](built-in-roles.md#billing-reader) z uživatele *Alain \@ example.com* v oboru skupiny pro správu.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Pokud se zobrazí chybová zpráva: "zadané informace nejsou namapovány na přiřazení role", ujistěte se, že jste zadali také `-Scope` `-ResourceGroupName` parametry nebo. Další informace najdete v tématu [řešení potíží s Azure RBAC](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="next-steps"></a>Další kroky

- [Vypsání přiřazení rolí Azure pomocí Azure PowerShell](role-assignments-list-powershell.md)
- [Kurz: udělení přístupu skupině k prostředkům Azure pomocí Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Správa prostředků pomocí Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)