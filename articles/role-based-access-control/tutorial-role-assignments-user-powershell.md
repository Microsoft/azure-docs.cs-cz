---
title: Kurz – Udělení přístupu pro uživatele pomocí RBAC a Azure PowerShellu | Microsoft Docs
description: Použijte řízení přístupu na základě role (RBAC) k udělení přístupu uživateli k zobrazení všeho v předplatném a správě všeho ve skupině prostředků pomocí Azure PowerShellu.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: 7f5e67edd2a5300ae1898e2620ee8ace80ac6d92
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109023"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-azure-powershell"></a>Kurz: Udělení přístupu pro uživatele pomocí RBAC a prostředí Azure PowerShell

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob správy přístupu k prostředkům v Azure. V tomto kurzu pomocí Azure PowerShellu udělíte uživateli přístup k zobrazení všeho v předplatném a správě všeho ve skupině prostředků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělení přístupu pro uživatele v různých oborech
> * Výpis přístupu
> * Odebrání přístupu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Oprávnění vytvářet uživatele v Azure Active Directory (nebo existujícího uživatele)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>Přiřazení rolí

V RBAC se přístup uděluje vytvořením přiřazení role. Přiřazení role se skládá ze tří prvků: objekt zabezpečení, definice role a obor. Tady jsou dvě přiřazení rolí, která v tomto kurzu provedete:

| Objekt zabezpečení | Definice role | Rozsah |
| --- | --- | --- |
| Uživatel<br>(RBAC Tutorial User) | [Čtenář](built-in-roles.md#reader) | Předplatné |
| Uživatel<br>(RBAC Tutorial User)| [Přispěvatel](built-in-roles.md#contributor) | Skupina prostředků<br>(rbac-tutorial-resource-group) |

   ![Přiřazení rolí pro uživatele](./media/tutorial-role-assignments-user-powershell/rbac-role-assignments-user.png)

## <a name="create-a-user"></a>Vytvoření uživatele

Pro přiřazení role potřebujete uživatele, skupinu nebo instanční objekt. Pokud uživatele ještě nemáte, můžete ho vytvořit.

1. Ve službě Azure Cloud Shell vytvořte heslo, které odpovídá vašim požadavkům na složitost hesla.

    ```azurepowershell
    $PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    $PasswordProfile.Password = "Password"
    ```

1. Vytvořte nového uživatele pro vaši doménu pomocí příkazu [New-AzureADUser](/powershell/module/azuread/new-azureaduser).

    ```azurepowershell
    New-AzureADUser -DisplayName "RBAC Tutorial User" -PasswordProfile $PasswordProfile `
      -UserPrincipalName "rbacuser@example.com" -AccountEnabled $true -MailNickName "rbacuser"
    ```
    
    ```Example
    ObjectId                             DisplayName        UserPrincipalName    UserType
    --------                             -----------        -----------------    --------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial User rbacuser@example.com Member
    ```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupinu prostředků použijeme k ukázce, jak přiřadit roli v oboru skupiny prostředků.

1. Získání seznamu sad oblast umístění s využitím [Get-AzLocation](/powershell/module/az.resources/get-azlocation) příkazu.

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. Vyberte umístění blízko vás a přiřaďte ho do proměnné.

   ```azurepowershell
   $location = "westus"
   ```

1. Vytvořit novou skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) příkazu.

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Udělení přístupu

Pokud chcete udělit přístup pro uživatele, můžete použít [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) příkazu přiřazení role. Musíte zadat objekt zabezpečení, definici role a obor.

1. Získejte ID vašeho předplatného pomocí [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) příkazu.

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Uložte obor předplatného do proměnné.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Přiřaďte uživateli roli [Čtenář](built-in-roles.md#reader) v oboru předplatného.

    ```azurepowershell
    New-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

1. Přiřaďte uživateli roli [Přispěvatel](built-in-roles.md#contributor) v oboru skupiny prostředků.

    ```azurepowershell
    New-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

## <a name="list-access"></a>Výpis přístupu

1. K ověření přístupu pro předplatné, použijte [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) zobrazte seznam přiřazení rolí.

    ```azurepowershell
    Get-AzRoleAssignment -SignInName rbacuser@example.com -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    Ve výstupu vidíte, že se uživateli RBAC Tutorial User přiřadila role Čtenář v oboru předplatného.

1. K ověření přístupu pro skupinu prostředků, použijte [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) zobrazte seznam přiřazení rolí.

    ```azurepowershell
    Get-AzRoleAssignment -SignInName rbacuser@example.com -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    Ve výstupu vidíte, že se uživateli RBAC Tutorial User přiřadily role Přispěvatel i Čtenář. Role Přispěvatel je v oboru skupiny prostředků rbac-tutorial-resource-group a role Čtenář se dědí v oboru předplatného.

## <a name="optional-list-access-using-the-azure-portal"></a>(Volitelné) Výpis přístupu pomocí webu Azure Portal

1. Pokud se chcete podívat, jak přiřazení rolí vypadají na webu Azure Portal, zobrazte okno **Řízení přístupu (IAM)** pro dané předplatné.

    ![Přiřazení rolí pro uživatele v oboru předplatného](./media/tutorial-role-assignments-user-powershell/role-assignments-subscription-user.png)

1. Zobrazte okno **Řízení přístupu (IAM)** pro danou skupinu prostředků.

    ![Přiřazení rolí pro uživatele v oboru skupiny prostředků](./media/tutorial-role-assignments-user-powershell/role-assignments-resource-group-user.png)

## <a name="remove-access"></a>Odebrání přístupu

Chcete-li odebrat přístup pro uživatele, skupiny nebo aplikace, použijte [odebrat AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) odebrat přiřazení role.

1. Pomocí následujícího příkazu odeberte uživateli přiřazení role Přispěvatel v oboru skupiny prostředků.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Pomocí následujícího příkazu odeberte uživateli přiřazení role Čtenář v oboru předplatného.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, odstraňte skupinu prostředků a uživatele.

1. Odstranit skupinu prostředků pomocí [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) příkazu.

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Když se zobrazí výzva k potvrzení, zadejte **Y**. Odstranění bude trvat několik sekund.

1. Odstraňte uživatele pomocí příkazu [Remove-AzureADUser](/powershell/module/azuread/remove-azureaduser).

    ```azurepowershell
    Remove-AzureADUser -ObjectId "rbacuser@example.com"
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa přístupu pomocí RBAC a PowerShellu](role-assignments-powershell.md)
