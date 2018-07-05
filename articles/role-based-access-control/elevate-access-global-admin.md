---
title: Zvýšení úrovně přístupu pro globálního správce ve službě Azure Active Directory | Dokumentace Microsoftu
description: Popisuje postup zvýšení úrovně přístupu pro globální správce v Azure Active Directory pomocí webu Azure portal nebo rozhraní REST API.
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
ms.date: 06/29/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0abf0a5971435fc3842a93e79d39468cba5c74da
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445207"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Zvýšení úrovně přístupu pro globálního správce ve službě Azure Active Directory

Pokud jste [globálního správce](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) ve službě Azure Active Directory (Azure AD), může nastat situace, kdy budete chtít provést následující:

- Pokud uživatel ztratí přístup znovu získat přístup k předplatnému Azure
- Udělit jiným uživatelem nebo sami přístup k předplatnému Azure
- Zobrazit všechna předplatná Azure v rámci organizace
- Povolit přístup všech předplatných Azure k automatizace aplikace (jako je například aplikace auditování nebo fakturace)

Ve výchozím nastavení, role správce Azure AD a Azure na základě rolí řízení přístupu role (RBAC) není span Azure AD a Azure. Pokud jste globální správce ve službě Azure AD, ale můžete zvýšit přístup ke správě předplatných Azure a skupiny pro správu. Když je zvýšení úrovně přístupu, budou mít [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) rolí (RBAC role) na všechna předplatná pro konkrétního tenanta. Role správce přístupu uživatelů umožňuje udělit jiným uživatelům přístup k prostředkům Azure v kořenovém oboru (`/`).

Toto zvýšení úrovně oprávnění by měl být dočasné a pouze v případě potřeby.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>Zvýšení úrovně přístupu pro globálního správce pomocí webu Azure portal

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) nebo [centra pro správu Azure Active Directory](https://aad.portal.azure.com).

1. V navigačním seznamu klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **vlastnosti**.

   ![Azure AD – vlastnosti – snímek obrazovky](./media/elevate-access-global-admin/aad-properties.png)

1. V části **globální správce může spravovat předplatná Azure a skupiny pro správu**, nastavte přepínač na **Ano**.

   ![Globální správce může spravovat předplatná Azure a skupiny pro správu – snímek obrazovky](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Pokud nastavíte přepínač na **Ano**, váš účet globálního správce (aktuálně přihlášeného uživatele) je přidán do role správce přístupu uživatelů v Azure RBAC v kořenovém oboru (`/`), která uděluje přístup k zobrazení a sestavu na Všechna předplatná Azure přidružené k tenantovi Azure AD.

   Pokud nastavíte přepínač na **ne**, váš účet globálního správce (aktuálně přihlášeného uživatele) je odebrán z role správce přístupu uživatelů v Azure RBAC. Nelze zobrazit všechna předplatná Azure, které jsou spojené s tenantem Azure AD a můžete zobrazit a spravovat pouze předplatná Azure ke kterým vám byl udělen přístup.

1. Klikněte na tlačítko **Uložit** uložte nastavení.

   Toto nastavení se nenachází ve vlastnosti globální a platí jenom pro aktuálně přihlášeného uživatele.

1. Provádění úloh, které je třeba provést na přístup se zvýšeným oprávněním. Jakmile budete hotovi, nastavte přepínač zpět **ne**.

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>Seznam přiřazení role v kořenovém oboru (/) pomocí Powershellu

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
```

## <a name="remove-a-role-assignment-at-the-root-scope--using-powershell"></a>Odebrání přiřazení role v kořenovém oboru (/) pomocí Powershellu

Chcete-li odebrat přiřazení role správce přístupu uživatelů pro uživatele v kořenovém oboru (`/`), použijte [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) příkazu.

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>Zvýšení úrovně přístupu pro globálního správce služby pomocí rozhraní REST API

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

## <a name="list-role-assignments-at-the-root-scope--using-the-rest-api"></a>Seznam přiřazení rolí v kořenovém oboru (/) pomocí rozhraní REST API

Můžete vytvořit seznam všechna přiřazení rolí pro uživatele v kořenovém oboru (`/`).

- Volání [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope) kde `{objectIdOfUser}` je ID objektu uživatele, jehož přiřazení rolí, které chcete načíst.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

## <a name="remove-elevated-access-using-the-rest-api"></a>Odebrat přístup se zvýšeným oprávněním pomocí rozhraní REST API

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

2. Potřebujete také seznam přiřazení role pro správce klienta v oboru tenanta. Seznam všech přiřazení v oboru tenanta pro `principalId` provedených volání zvýšení přístupu správce tenanta. Tím se zobrazí seznam všech přiřazení v tenantovi pro objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Správce tenanta by neměl mít mnoho přiřazení, pokud předchozí dotaz vrací příliš mnoho přiřazení, můžete také zadávat dotazy pro všechna přiřazení jenom na úrovni oboru tenanta a potom vyfiltrujte z výsledků: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Předchozí volání vrátí seznam přiřazení rolí. Vyhledejte přiřazení role, kde obor je `"/"` a `roleDefinitionId` končí ID názvu role jste získali v kroku 1 a `principalId` odpovídá objectId správce tenanta. 
    
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

- [Řízení přístupu podle role pomocí REST](role-assignments-rest.md)
- [Správa přístupu k prostředkům Azure se službou Privileged Identity Management](pim-azure-resource.md)
- [Správa přístupu ke správě Azure s podmíněným přístupem](conditional-access-azure-management.md)
