---
title: Zvýšení úrovně přístupu ke správě všech předplatných Azure a skupiny pro správu | Dokumentace Microsoftu
description: Popisuje postup zvýšení úrovně přístupu pro globální správce ke správě všech předplatných a skupin pro správu v Azure Active Directory pomocí webu Azure portal nebo rozhraní REST API.
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
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 7552018c32078295c164023f909a604c6522c32f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437466"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Zvýšení úrovně přístupu ke správě všech předplatných Azure a skupiny pro správu

Jako globální správce ve službě Azure Active Directory (Azure AD) nebudete mít přístup ke všem předplatných a skupin pro správu ve vašem adresáři. Tento článek popisuje způsoby, můžete zvýšit váš přístup na všechna předplatná a skupiny pro správu.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Proč potřebují k rozvoji vaší přístup?

Pokud jste globální správce, může nastat situace, kdy chcete provést následující kroky:

- Pokud uživatel ztratí přístup jak opět získat přístup do Azure předplatné nebo skupinu pro správu
- Udělit jiným uživatelem nebo sami přístup do Azure předplatné nebo skupinu pro správu
- Zobrazit všechna předplatná Azure nebo skupiny pro správu v organizaci
- Povolit aplikaci služby automation (jako je například aplikace auditování nebo fakturace) pro přístup k Všechna předplatná Azure nebo skupiny pro správu

## <a name="how-does-elevate-access-work"></a>Jak zvýšit přístup do práce?

Azure AD a prostředky Azure jsou zabezpečené nezávisle na sobě navzájem. To znamená přiřazení role Azure AD bez možnosti udělovat přístup k prostředkům Azure, a přiřazení Azure role bez možnosti udělovat přístup ke službě Azure AD. Ale pokud jste [globálního správce](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) ve službě Azure AD, můžete přiřadit sami přístup ke všem předplatným Azure a skupiny pro správu ve vašem adresáři. Tuto funkci použijte, pokud nemáte přístup k prostředkům předplatného Azure, jako jsou virtuální počítače nebo účty úložiště, a chcete použít k získání přístupu k těmto prostředkům vaše oprávnění globálního správce.

Když je zvýšení úrovně přístupu, se vám přidělí [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) role v Azure na kořenového oboru (`/`). To umožňuje zobrazit všechny prostředky a přiřadit přístup v libovolné předplatné nebo skupinu pro správu v adresáři. Přiřazení rolí správce uživatelských přístupů lze odebrat pomocí Powershellu.

Po provedení změny, které je třeba provést na kořenového oboru, měli byste odebrat tento přístup se zvýšeným oprávněním.

![Zvýšení úrovně přístupu](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>portál Azure

Použijte následující postup zvýšení úrovně přístupu pro globálního správce pomocí webu Azure portal.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) nebo [centra pro správu Azure Active Directory](https://aad.portal.azure.com) jako globální správce.

1. V navigačním seznamu klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **vlastnosti**.

   ![Azure AD – vlastnosti – snímek obrazovky](./media/elevate-access-global-admin/aad-properties.png)

1. V části **Access management pro prostředky Azure**, nastavte přepínač na **Ano**.

   ![Správa přístupu pro prostředky Azure – snímek obrazovky](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Pokud nastavíte přepínač na **Ano**, mají přiřazenou roli správce uživatelských přístupů v Azure RBAC v kořenovém oboru (/). To vám uděluje oprávnění k přiřazení rolí ve všech předplatných Azure a skupiny pro správu přidružené k tento adresář Azure AD. Tento přepínač je pouze dostupné pro uživatele, kteří mají přiřazenou roli globálního správce ve službě Azure AD.

   Pokud nastavíte přepínač na **ne**, role správce přístupu uživatelů v Azure RBAC je odebraný z vašeho účtu uživatele. Už můžete přiřadit role ve všech předplatných Azure a skupiny pro správu, které jsou přidružené k tomuto adresáři Azure AD. Můžete zobrazit a spravovat pouze předplatná Azure a skupiny pro správu ke kterým vám byl udělen přístup.

1. Klikněte na tlačítko **Uložit** uložte nastavení.

   Toto nastavení se nenachází ve vlastnosti globální a platí jenom pro aktuálně přihlášeného uživatele. Nelze zvýšení úrovně přístupu pro všechny členy rolí globálního správce.

1. Odhlaste se a znovu se přihlaste k aktualizaci vašeho přístupu.

    Nyní byste měli mít přístup ke všem předplatných a skupin pro správu ve vašem adresáři. Můžete si všimnout, že mají přiřazenou roli správce uživatelských přístupů na kořenového oboru.

   ![Přiřazení rolí předplatné s kořenového oboru – snímek obrazovky](./media/elevate-access-global-admin/iam-root.png)

1. Změny, které je třeba provést na přístup se zvýšeným oprávněním.

    Informace o přiřazování rolí najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](role-assignments-portal.md). Pokud používáte Azure AD Privileged Identity Management (PIM), přečtěte si téma [prostředků zjistit Azure ke správě v PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) nebo [role prostředků Azure přiřadit v PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Jakmile budete hotovi, nastavte **Access management pro prostředky Azure** přepnete zpět do **ne**. Vzhledem k tomu, že toto je uživatelská nastavení, které musíte být přihlášeni pod stejným uživatelem používala pro zvýšení úrovně přístupu.

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-role-assignment-at-the-root-scope-"></a>Seznam přiřazení role v kořenovém oboru (/)

Seznam přiřazení role správce přístupu uživatelů pro uživatele v kořenovém oboru (`/`), použijte [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) příkazu.

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>Odebrání přiřazení role v kořenovém oboru (/)

Chcete-li odebrat přiřazení role správce přístupu uživatelů pro uživatele v kořenovém oboru (`/`), postupujte podle těchto kroků.

1. Přihlaste se jako uživatel, který může odebrat přístup se zvýšeným oprávněním. Může to být stejný uživatel, který byl použitý ke zvýšení oprávnění přístupu nebo jiného globálního správce se zvýšenými oprávněními přístup v kořenovém oboru.


1. Použití [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) příkaz pro odebrání přiřazení role správce přístupu uživatelů.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>Zvýšení úrovně přístupu pro globální správce

Použijte následující základní kroky pro zvýšení úrovně přístupu pro globálního správce služby pomocí rozhraní REST API.

1. Pomocí rozhraní REST, volání `elevateAccess`, která uděluje role správce přístupu uživatelů v kořenovém oboru (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Vytvoření [přiřazení role](/rest/api/authorization/roleassignments) jakoukoli roli na žádný rozsah přiřazení. V následujícím příkladu jsou uvedeny vlastnosti pro roli {roleDefinitionID} v kořenovém oboru přiřazení (`/`):

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. Při přístupu správce uživatelů, můžete také odebrat přiřazení rolí v kořenovém oboru (`/`).

1. Váš správce uživatelských přístupů oprávnění odeberte, dokud je budete potřebovat znovu.

### <a name="list-role-assignments-at-the-root-scope-"></a>Seznam přiřazení rolí v kořenovém oboru (/)

Můžete vytvořit seznam všechna přiřazení rolí pro uživatele v kořenovém oboru (`/`).

- Volání [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope) kde `{objectIdOfUser}` je ID objektu uživatele, jehož přiřazení rolí, které chcete načíst.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>Seznam zamítnout přiřazení v kořenovém oboru (/)

Můžete vyvolat seznam všech přiřazení odepřít pro uživatele v kořenovém oboru (`/`).

- Volejte GET denyAssignments kde `{objectIdOfUser}` je ID objektu uživatele, jehož zamítnout přiřazení, které chcete načíst.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Odebrat přístup se zvýšeným oprávněním

Při volání `elevateAccess`, vytvořit přiřazení role pro sebe, takže se odvolat těchto oprávnění budete muset odebrat přiřazení.

1. Volání [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) kde `roleName` správce přístupu uživatelů k určení ID názvu role správce uživatelských přístupů rovná se.

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

    ID z si pak uložte `name` parametrů v tomto případě `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. Potřebujete také seznam přiřazení role pro správce adresáře v adresáři oboru. Seznam všech přiřazení v oboru adresář pro `principalId` správce adresáře, který provedené zvýšení přístupu volání. Tím se zobrazí seznam všech přiřazení v adresáři pro objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Správce adresáře by neměly mít mnoho přiřazení, pokud předchozí dotaz vrací příliš mnoho přiřazení, můžete také zadávat dotazy pro všechna přiřazení jenom na úrovni oboru adresáře a potom vyfiltrujte z výsledků: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Předchozí volání vrátí seznam přiřazení rolí. Vyhledejte přiřazení role, kde obor je `"/"` a `roleDefinitionId` končí ID názvu role jste získali v kroku 1 a `principalId` odpovídá objectId správce adresáře. 
    
    Ukázka přiřazení role:

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
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    Znovu uložit ID z `name` parametr, v tomto případě e7dd75bc-06f6-4e71-9014-ee96a929d099.

    3. Nakonec použijte ID přiřazení role se odebrat přiřazení přidal `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Další postup

- [Vysvětlení různých rolí](rbac-and-directory-admin-roles.md)
- [Řízení přístupu podle role pomocí REST](role-assignments-rest.md)
