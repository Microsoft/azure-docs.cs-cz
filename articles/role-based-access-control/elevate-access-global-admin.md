---
title: Zvýšení úrovně přístupu pro správu všech předplatných Azure a skupin pro správu
description: Popisuje, jak zvýšit přístup globálního správce ke správě všech předplatných a skupin pro správu ve službě Azure Active Directory pomocí portálu Azure nebo rozhraní REST API.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 18490ec7c3cfde33cef186b753e2adc809f854c6
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641357"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Zvýšení úrovně přístupu pro správu všech předplatných Azure a skupin pro správu

Jako globální správce ve službě Azure Active Directory (Azure AD) nemusíte mít ve svém adresáři přístup ke všem předplatným a skupinám pro správu. Tento článek popisuje způsoby, jak můžete zvýšit přístup ke všem předplatným a skupinám pro správu.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Proč bys měl zvýšit přístup?

Pokud jste globální správce, může nastat doba, kdy budete chtít provést následující akce:

- Opětovné získání přístupu k předplatnému nebo skupině pro správu Azure, když uživatel ztratí přístup
- udělit sobě nebo jinému uživateli přístup k předplatnému Azure nebo skupině pro správu,
- Zobrazení všech předplatných nebo skupin pro správu Azure v organizaci
- Povolení aplikace pro automatizaci (například fakturační nebo auditovací aplikace) pro přístup ke všem předplatným nebo skupinám pro správu Azure

## <a name="how-does-elevated-access-work"></a>Jak funguje zvýšený přístup?

Prostředky Azure AD a Azure jsou zabezpečené nezávisle na sobě. To znamená, že přiřazení rolí Azure AD neudělují přístup k prostředkům Azure a přiřazení rolí Azure neudělují přístup k Azure AD. Pokud jste však [globální správce](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) ve službě Azure AD, můžete si přiřadit přístup ke všem předplatným Azure a skupinám pro správu ve vašem adresáři. Tuto možnost použijte, pokud nemáte přístup k prostředkům předplatného Azure, jako jsou virtuální počítače nebo účty úložiště, a chcete použít oprávnění globálního správce k získání přístupu k těmto prostředkům.

Když zvýšíte přístup, bude vám přiřazena role [správce přístupu uživatelů](built-in-roles.md#user-access-administrator) v Azure v kořenovém oboru (`/`).To umožňuje zobrazit všechny prostředky a přiřadit přístup v libovolném předplatném nebo skupině pro správu v adresáři. Přiřazení rolí správce přístupu uživatelů lze odebrat pomocí Azure PowerShell, Azure CLI nebo rozhraní REST API.

Tento zvýšený přístup byste měli odebrat, jakmile provedete změny, které je třeba provést v kořenovém oboru.

![Zvýšení přístupu](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>portál Azure

### <a name="elevate-access-for-a-global-administrator"></a>Zvýšení přístupu pro globálního správce

Podle těchto kroků zvýšit přístup pro globálního správce pomocí portálu Azure.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com) nebo [centru pro správu Azure Active Directory](https://aad.portal.azure.com) jako globální správce.

1. Otevřete **službu Azure Active Directory**.

1. V části **Manage**vyberte **Vlastnosti**.

   ![Výběr vlastností pro vlastnosti služby Azure Active Directory – snímek obrazovky](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. V části **Správa přístupu pro prostředky Azure**nastavte přepínač na **Ano**.

   ![Správa přístupu pro prostředky Azure – snímek obrazovky](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Když nastavíte přepínač na **Ano**, je vám přiřazena role správce přístupu uživatelů v Azure RBAC v kořenovém oboru (/). To vám uděluje oprávnění k přiřazení rolí ve všech předplatných Azure a skupin pro správu přidružených k tomuto adresáři Azure AD. Tento přepínač je k dispozici jenom pro uživatele, kterým je přiřazena role globálního správce ve službě Azure AD.

   Když nastavíte přepínač na **Ne**, role Správce přístupu uživatelů v Azure RBAC se odebere z vašeho uživatelského účtu. Už nemůžete přiřazovat role ve všech předplatných Azure a skupinách pro správu, které jsou přidružené k tomuto adresáři Azure AD. Můžete zobrazit a spravovat jenom předplatná Azure a skupiny pro správu, ke kterým jste získali přístup.

    > [!NOTE]
    > Pokud používáte [Azure AD Privileged Identity Management (PIM),](../active-directory/privileged-identity-management/pim-configure.md)deaktivace přiřazení role nezmění tento přepínač na **Ne**. Chcete-li zachovat nejméně privilegovaný přístup, doporučujeme nastavit tento přepínač na **ne** před deaktivací přiřazení role.
    
1. Kliknutím na **Uložit** nastavení uložte.

   Toto nastavení není globální vlastností a vztahuje se pouze na aktuálně přihlášeného uživatele. Nelze zvýšit přístup pro všechny členy role globálního správce.

1. Odhlaste se a přihlaste se a aktualizujte přístup.

    Nyní byste měli mít přístup ke všem předplatným a skupinám pro správu ve vašem adresáři. Při zobrazení podokna řízení přístupu (IAM) zjistíte, že vám byla přiřazena role Správce přístupu uživatelů v kořenovém oboru.

   ![Přiřazení rolí předplatného s kořenovým oborem – snímek obrazovky](./media/elevate-access-global-admin/iam-root.png)

1. Proveďte změny, které je třeba provést při zvýšeném přístupu.

    Informace o přiřazování rolí najdete [v tématu Správa přístupu pomocí RBAC a portálu Azure](role-assignments-portal.md). Pokud používáte Azure AD Privileged Identity Management (PIM), [přečtěte si část Discover Azure resources to manage in PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) or [Assign Azure resource roles in PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

### <a name="remove-elevated-access"></a>Odebrání přístupu se zvýšenými oprávněními

Chcete-li odebrat přiřazení role Správce`/`přístupu uživatelů v kořenovém oboru ( ), postupujte takto.

1. Přihlaste se jako stejný uživatel, který byl použit ke zvýšení přístupu.

1. V navigačním seznamu klikněte na **Azure Active Directory** a potom klikněte na **vlastnosti**.

1. Nastavte **správu přístupu pro prostředky Azure** přepnout zpět na **ne**. Vzhledem k tomu, že se jedná o nastavení pro jednotlivé uživatele, musíte být přihlášeni jako stejný uživatel, který byl použit ke zvýšení oprávnění.

    Pokud se pokusíte odebrat přiřazení role Správce uživatelského přístupu v podokně řízení přístupu (IAM), zobrazí se následující zpráva. Chcete-li odebrat přiřazení role, musíte nastavit přepínač zpět na **Ne** nebo použít Azure PowerShell, Azure CLI nebo rozhraní REST API.

    ![Odebrání přiřazení rolí s kořenovým oborem](./media/elevate-access-global-admin/iam-root-remove.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Přiřazení role seznamu v kořenovém oboru (/)

Chcete-li vypsat přiřazení role Správce`/`přístupu uživatelů pro uživatele v kořenovém oboru ( ), použijte příkaz [Get-AzRoleAssignment.](/powershell/module/az.resources/get-azroleassignment)

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>Odebrání přístupu se zvýšenými oprávněními

Chcete-li odebrat přiřazení role Správce přístupu uživatelů`/`pro sebe nebo jiného uživatele v kořenovém oboru ( ), postupujte takto.

1. Přihlaste se jako uživatel, který může odebrat přístup se zvýšenými oprávněními. Může se jedná o stejného uživatele, který byl použit ke zvýšení přístupu, nebo jiného globálního správce se zvýšeným přístupem v kořenovém oboru.

1. Pomocí příkazu [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) odeberte přiřazení role Správce přístupu uživatelů.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-role-assignment-at-root-scope-"></a>Přiřazení role seznamu v kořenovém oboru (/)

Chcete-li vypsat přiřazení role Správce`/`přístupu uživatelů pro uživatele v kořenovém oboru ( ), použijte příkaz [seznamu přiřazení role az.](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>Odebrání přístupu se zvýšenými oprávněními

Chcete-li odebrat přiřazení role Správce přístupu uživatelů`/`pro sebe nebo jiného uživatele v kořenovém oboru ( ), postupujte takto.

1. Přihlaste se jako uživatel, který může odebrat přístup se zvýšenými oprávněními. Může se jedná o stejného uživatele, který byl použit ke zvýšení přístupu, nebo jiného globálního správce se zvýšeným přístupem v kořenovém oboru.

1. Pomocí příkazu [odstranění přiřazení role az](/cli/azure/role/assignment#az-role-assignment-delete) odeberte přiřazení role Správce přístupu uživatelů.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>Zvýšení přístupu pro globálního správce

Pomocí následujících základních kroků zvýšit přístup pro globálního správce pomocí rozhraní REST API.

1. Pomocí funkce `elevateAccess`REST volejte , která vám udělí`/`roli správce přístupu uživatelů v kořenovém oboru ( ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Vytvořte [přiřazení role](/rest/api/authorization/roleassignments) pro přiřazení libovolné role v libovolném oboru. Následující příklad ukazuje vlastnosti pro přiřazení role {roledefinitionID}`/`v kořenovém oboru ( ):

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "11111111-1111-1111-1111-111111111111"
   }
   ```

1. Během funkce správce přístupu uživatele můžete také odebrat`/`přiřazení rolí v kořenovém oboru ( ).

1. Odeberte oprávnění správce přístupu uživatelů, dokud je znovu nepotřebujete.

### <a name="list-role-assignments-at-root-scope-"></a>Seznam přiřazení rolí v kořenovém oboru (/)

Můžete uvést všechny přiřazení rolí pro uživatele v`/`kořenovém oboru ( ).

- Volání [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope) where `{objectIdOfUser}` is the object ID of the user whose role assignments you want to retrieve.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Vypsat přiřazení v kořenovém oboru (/)

Můžete vypsat všechna přiřazení odepření pro`/`uživatele v kořenovém oboru ( ).

- Volání GET denyAssignments kde `{objectIdOfUser}` je ID objektu uživatele, jehož odepřít přiřazení, které chcete načíst.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Odebrání přístupu se zvýšenými oprávněními

Při volání `elevateAccess`vytvoříte přiřazení role pro sebe, abyste odvolali tato oprávnění, která potřebujete k odebrání`/`přiřazení role Správce přístupu uživatelů pro sebe v kořenovém oboru ( ).

1. Volání [get roleDefinitions](/rest/api/authorization/roledefinitions/get) kde `roleName` se rovná správce přístupu uživatele k určení ID názvu role správce přístupu uživatele.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Uložte ID `name` z parametru, `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`v tomto případě .

1. Je také nutné uvést přiřazení role pro správce adresáře v oboru adresáře. Seznam všech přiřazení v oboru `principalId` adresáře pro správce adresáře, který provedl volání služby převádšit přístup. To bude seznam všech přiřazení v adresáři pro objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Správce adresáře by neměl mít mnoho přiřazení, pokud předchozí dotaz vrátí příliš mnoho přiřazení, můžete také dotazovat na všechna přiřazení pouze na úrovni oboru adresáře a potom filtrovat výsledky:`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. Předchozí volání vrátí seznam přiřazení rolí. Najděte přiřazení role, `"/"` kde `roleDefinitionId` je obor a končí ID názvu `principalId` role, které jste našli v kroku 1 a odpovídá objektuId správce adresáře. 
    
    Přiřazení ukázkové role:
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    Opět uložte ID `name` z parametru, v tomto případě 11111111-1111-1111-1111-1111-11111111111111.

1. Nakonec pomocí ID přiřazení role odeberte `elevateAccess`přiřazení přidané :

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Další kroky

- [Vysvětlení různých rolí v Azure](rbac-and-directory-admin-roles.md)
- [Správa přístupu k prostředkům Azure pomocí RBAC a rozhraní REST API](role-assignments-rest.md)
