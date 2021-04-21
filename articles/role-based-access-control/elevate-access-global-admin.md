---
title: Zvýšení úrovně přístupu pro správu všech předplatných Azure a skupin pro správu
description: V této části najdete popis postupu při zvýšení přístupu globálního správce ke správě všech předplatných a skupin pro správu v Azure Active Directory pomocí Azure Portal nebo REST API.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/09/2020
ms.author: rolyon
ms.openlocfilehash: 37d50c030a2b426cb3e9af57afb899b7fab68388
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778465"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Zvýšení úrovně přístupu pro správu všech předplatných Azure a skupin pro správu

Jako globální správce v Azure Active Directory (Azure AD) možná nemáte přístup ke všem předplatným a skupinám pro správu v adresáři. Tento článek popisuje způsoby, kterými můžete zvýšit úroveň přístupu ke všem předplatným a skupinám pro správu.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Proč byste měli zvýšit svůj přístup?

Pokud jste globální správce, může nastat situace, kdy budete chtít provést následující akce:

- Opětovné získání přístupu k předplatnému Azure nebo skupině pro správu, když uživatel ztratí přístup
- udělit sobě nebo jinému uživateli přístup k předplatnému Azure nebo skupině pro správu,
- Zobrazit všechna předplatná Azure nebo skupiny pro správu v organizaci
- Povolení aplikace automatizace (například fakturace nebo aplikace auditování) pro přístup ke všem předplatným Azure nebo skupinám pro správu

## <a name="how-does-elevated-access-work"></a>Jak funguje zvýšený přístup?

Prostředky Azure AD a Azure jsou zabezpečené nezávisle na sobě. To znamená, že přiřazení rolí Azure AD neudělí přístup k prostředkům Azure a přiřazení rolí Azure neuděluje přístup ke službě Azure AD. Pokud jste však [globálním správcem](../active-directory/roles/permissions-reference.md#global-administrator) služby Azure AD, můžete sami sobě přiřadit přístup ke všem předplatným Azure a skupinám pro správu v adresáři. Tuto možnost použijte, pokud nemáte přístup k prostředkům předplatného Azure, jako jsou například virtuální počítače nebo účty úložiště, a chcete pro získání přístupu k těmto prostředkům použít globální oprávnění správce.

Po zvýšení úrovně přístupu vám bude přiřazena role [Správce přístupu uživatele](built-in-roles.md#user-access-administrator) v Azure v kořenovém oboru ( `/` ).To vám umožní zobrazit všechny prostředky a přiřadit přístup v rámci předplatného nebo skupiny pro správu v adresáři. Přiřazení role správce přístupu uživatele můžete odebrat pomocí Azure PowerShell, Azure CLI nebo REST API.

Tento přístup se zvýšeným oprávněním byste měli odebrat, jakmile provedete změny, které potřebujete udělat v kořenovém oboru.

![Zvýšení přístupu](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>portál Azure

### <a name="elevate-access-for-a-global-administrator"></a>Zvýšení přístupu pro globálního správce

Pomocí těchto kroků můžete zvýšit přístup pro globálního správce pomocí Azure Portal.

1. Přihlaste se do [Azure Portal](https://portal.azure.com) nebo do [centra pro správu Azure Active Directory](https://aad.portal.azure.com) jako globální správce.

    Pokud používáte Azure AD Privileged Identity Management, [aktivujte přiřazení role globálního správce](../active-directory/privileged-identity-management/pim-how-to-activate-role.md).

1. Otevřete **Azure Active Directory**.

1. V části **Spravovat** vyberte **Vlastnosti**.

   ![Vybrat vlastnosti pro Azure Active Directory vlastnosti – snímek obrazovky](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. V části **Správa přístupu pro prostředky Azure** nastavte přepínač na **Ano**.

   ![Správa přístupu pro prostředky Azure – snímek obrazovky](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Když nastavíte přepínač na **Ano**, bude vám přiřazena role správce přístupu uživatele v Azure RBAC v kořenovém oboru (/). Tím udělíte oprávnění k přiřazování rolí ve všech předplatných Azure a skupinách pro správu přidružených k tomuto adresáři služby Azure AD. Tento přepínač je dostupný jenom uživatelům, kteří mají přiřazenou roli globálního správce ve službě Azure AD.

   Když nastavíte přepínač na **ne**, role správce přístupu uživatele v Azure RBAC se odebere z vašeho uživatelského účtu. Ve všech předplatných Azure a skupinách pro správu, které jsou přidružené k tomuto adresáři služby Azure AD, už nemůžete přiřazovat role. Můžete zobrazit a spravovat pouze předplatná Azure a skupiny pro správu, kterým jste udělili přístup.

    > [!NOTE]
    > Pokud používáte [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md), deaktivace přiřazení role nezmění přepínač **Správa přístupu pro prostředky Azure** na **ne**. Pokud chcete zachovat nejméně privilegovaný přístup, doporučujeme nastavit tento přepínač na **ne** , než deaktivujete přiřazení role.
    
1. Uložte nastavení kliknutím na **Uložit** .

   Toto nastavení není globálních vlastností a platí pouze pro aktuálně přihlášeného uživatele. Přístup pro všechny členy role globálního správce nelze zvýšit.

1. Odhlaste se a znovu se přihlaste, abyste mohli aktualizovat svůj přístup.

    Nyní byste měli mít přístup ke všem předplatným a skupinám pro správu v adresáři. Po zobrazení podokna řízení přístupu (IAM) si všimnete, že jste byli přiřazeni role správce přístupu uživatele v kořenovém oboru.

   ![Přiřazení role předplatného s kořenovým oborem – snímek obrazovky](./media/elevate-access-global-admin/iam-root.png)

1. Proveďte změny, které potřebujete k zajištění vyššího přístupu.

    Informace o přiřazování rolí najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](role-assignments-portal.md). Pokud používáte Privileged Identity Management, přečtěte si téma [zjišťování prostředků Azure pro správu](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) nebo [přiřazení rolí prostředků Azure](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Provedením kroků v následující části odeberte váš přístup se zvýšenými oprávněními.

### <a name="remove-elevated-access"></a>Odebrat vyšší úroveň přístupu

Chcete-li odebrat přiřazení role správce přístupu uživatele v kořenovém oboru ( `/` ), postupujte podle těchto kroků.

1. Přihlaste se jako stejný uživatel, který se použil k zvýšení přístupu.

1. V navigačním seznamu klikněte na **Azure Active Directory** a pak klikněte na **vlastnosti**.

1. Nastavení **řízení přístupu pro prostředky Azure** přepnete zpátky na **ne**. Vzhledem k tomu, že se jedná o nastavení pro jednotlivé uživatele, musíte být přihlášeni jako stejný uživatel, jako jste použili k zvýšení přístupu.

    Pokud se pokusíte odebrat přiřazení role správce přístupu uživatele v podokně řízení přístupu (IAM), zobrazí se následující zpráva. Pokud chcete přiřazení role odebrat, musíte nastavit přepínač zpět na **ne** nebo použít Azure PowerShell, Azure CLI nebo REST API.

    ![Odebrání přiřazení rolí s kořenovým oborem](./media/elevate-access-global-admin/iam-root-remove.png)

1. Odhlaste se jako globální správce.

    Pokud používáte Privileged Identity Management, deaktivujte své přiřazení role globálního správce.

    > [!NOTE]
    > Pokud používáte [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md), deaktivace přiřazení role nezmění přepínač **Správa přístupu pro prostředky Azure** na **ne**. Pokud chcete zachovat nejméně privilegovaný přístup, doporučujeme nastavit tento přepínač na **ne** , než deaktivujete přiřazení role.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Vypsat přiřazení role v kořenovém oboru (/)

K vypsání přiřazení role správce přístupu uživatele pro uživatele v kořenovém oboru ( `/` ) použijte příkaz [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) .

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

### <a name="remove-elevated-access"></a>Odebrat vyšší úroveň přístupu

Chcete-li odebrat přiřazení role správce přístupu uživatele pro sebe nebo jiného uživatele v kořenovém oboru ( `/` ), postupujte podle těchto kroků.

1. Přihlaste se jako uživatel, který může odebrat zvýšený přístup. Může to být stejný uživatel, který se použil ke zvýšení přístupu nebo jiného globálního správce se zvýšeným přístupem v kořenovém oboru.

1. K odebrání přiřazení role správce přístupu uživatele použijte příkaz [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) .

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="elevate-access-for-a-global-administrator"></a>Zvýšení přístupu pro globálního správce

Pomocí následujících základních kroků můžete zvýšit přístup pro globálního správce pomocí Azure CLI.

1. Pomocí příkazu [AZ REST](/cli/azure/reference-index#az_rest) zavolejte `elevateAccess` koncový bod, který uděluje roli správce přístupu uživatele v kořenovém oboru ( `/` ).

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. Proveďte změny, které potřebujete k zajištění vyššího přístupu.

    Informace o přiřazování rolí najdete v tématu [přiřazení rolí Azure pomocí Azure CLI](role-assignments-cli.md).

1. Provedením kroků v pozdější části odeberte váš přístup se zvýšenými oprávněními.

### <a name="list-role-assignment-at-root-scope-"></a>Vypsat přiřazení role v kořenovém oboru (/)

Chcete-li zobrazit seznam přiřazení role správce přístupu uživatele pro uživatele v kořenovém oboru ( `/` ), použijte příkaz [AZ role Assignment list](/cli/azure/role/assignment#az_role_assignment_list) .

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

### <a name="remove-elevated-access"></a>Odebrat vyšší úroveň přístupu

Chcete-li odebrat přiřazení role správce přístupu uživatele pro sebe nebo jiného uživatele v kořenovém oboru ( `/` ), postupujte podle těchto kroků.

1. Přihlaste se jako uživatel, který může odebrat zvýšený přístup. Může to být stejný uživatel, který se použil ke zvýšení přístupu nebo jiného globálního správce se zvýšeným přístupem v kořenovém oboru.

1. Pomocí příkazu [AZ role Assignment Delete](/cli/azure/role/assignment#az_role_assignment_delete) odeberte přiřazení role správce přístupu uživatele.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>Rozhraní REST API

### <a name="elevate-access-for-a-global-administrator"></a>Zvýšení přístupu pro globálního správce

Pomocí následujících základních kroků můžete zvýšit přístup pro globálního správce pomocí REST API.

1. Pomocí REST volejte volání `elevateAccess` , které uděluje roli správce přístupu uživatele v kořenovém oboru ( `/` ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Proveďte změny, které potřebujete k zajištění vyššího přístupu.

    Informace o přiřazování rolí najdete v tématu [přiřazení rolí Azure pomocí REST API](role-assignments-rest.md).

1. Provedením kroků v pozdější části odeberte váš přístup se zvýšenými oprávněními.

### <a name="list-role-assignments-at-root-scope-"></a>Vypsat přiřazení rolí v kořenovém oboru (/)

Můžete vypsat všechna přiřazení rolí pro uživatele v kořenovém oboru ( `/` ).

- Zavolejte [Get roleAssignments](/rest/api/authorization/roleassignments/listforscope) , kde `{objectIdOfUser}` je ID objektu uživatele, jehož přiřazení role chcete načíst.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Vypsat přiřazení zamítnutí v kořenovém oboru (/)

Můžete vypsat všechna přiřazení zamítnutí pro uživatele v kořenovém oboru ( `/` ).

- Zavolejte GET denyAssignments, kde `{objectIdOfUser}` je ID objektu uživatele, jehož přiřazení zamítnout chcete načíst.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Odebrat vyšší úroveň přístupu

Když zavoláte `elevateAccess` , vytvoříte přiřazení role sami, takže odvoláte tato oprávnění, která potřebujete k odebrání přiřazení role správce přístupu uživatele v kořenovém oboru ( `/` ).

1. Pokud [](/rest/api/authorization/roledefinitions/get) `roleName` chcete zjistit ID názvu role správce přístupu uživatele, zavolejte funkci get roleDefinitions, kde se rovná správce přístupu uživatele.

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

    `name`V tomto případě uložte ID z parametru `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` .

1. Musíte také uvést přiřazení role pro správce adresáře v oboru adresáře. Vypíše všechna přiřazení v oboru adresáře pro `principalId` Správce adresáře, který provedl volání přístupu ke zvýšení oprávnění. Zobrazí se seznam všech přiřazení v adresáři pro identifikátor objectID.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Správce adresáře by neměl mít mnoho přiřazení, pokud předchozí dotaz vrátí příliš mnoho přiřazení, můžete také zadat dotaz na všechna přiřazení pouze v úrovni oboru adresáře a následně vyfiltrovat výsledky: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. Předchozí volání vrátí seznam přiřazení rolí. Vyhledejte přiřazení role, kde je rozsah `"/"` a `roleDefinitionId` končí s ID názvu role, které jste našli v kroku 1 a `principalId` odpovídá objectID Správce adresáře. 
    
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
    
    Znovu uložte ID z `name` parametru, v tomto případě 11111111-1111-1111-1111-111111111111.

1. Nakonec pomocí ID přiřazení role odeberte přiřazení, které přidal `elevateAccess` :

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Další kroky

- [Vysvětlení různých rolí](rbac-and-directory-admin-roles.md)
- [Přiřazení rolí Azure pomocí REST API](role-assignments-rest.md)
