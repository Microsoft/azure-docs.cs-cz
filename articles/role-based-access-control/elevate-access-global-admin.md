---
title: Zvýšení oprávnění přístupu pro globálního správce ve službě Azure Active Directory | Microsoft Docs
description: Popisuje, jak ke zvýšení oprávnění přístupu pro globálního správce ve službě Azure Active Directory pomocí portálu Azure nebo REST API.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.openlocfilehash: b671ff6b473093e59bce18c7bf98b32e9849bbb0
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Zvýšení oprávnění přístupu pro globálního správce ve službě Azure Active Directory

Pokud jste [globálního správce](../active-directory/active-directory-assign-admin-roles-azure-portal.md#global-administrator) v Azure Active Directory (Azure AD), nemusí být vždy, když chcete provést následující:

- Znovu získat přístup k předplatnému Azure, když uživatel ztratí přístup
- Udělit jiným uživatelem nebo sami přístup k předplatnému Azure
- Zobrazit všechny odběry služby Azure v organizaci
- Povolit přístup k všechny odběry služby Azure automation aplikace (například fakturace nebo auditování aplikace)

Ve výchozím nastavení řízení rolí Správce služby Azure AD a přístupu na základě role Azure role (RBAC) není rozpětí Azure AD a Azure. Pokud jste globálním správcem ve službě Azure AD, můžete však zvýšení oprávnění přístup ke správě předplatných Azure a skupiny pro správu. Pokud jste zvýšení vaší přístupu, udělí se vám [správce přístupu uživatelů](built-in-roles.md#user-access-administrator) role (RBAC role) na všechny odběry pro konkrétního klienta. Role správce přístupu uživatelů umožňuje jiných uživatelům uděluje přístup k prostředkům Azure v kořenovém oboru (`/`).

Toto zvýšení úrovně oprávnění by měl být dočasné a pouze v případě potřeby.

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>Zvýšení oprávnění přístupu pro globálního správce pomocí portálu Azure

1. Přihlaste se k [portál Azure](https://portal.azure.com) nebo [centra pro správu Azure Active Directory](https://aad.portal.azure.com).

1. V seznamu navigace klikněte na **Azure Active Directory** a pak klikněte na **vlastnosti**.

   ![Azure AD – vlastnosti – snímek obrazovky](./media/elevate-access-global-admin/aad-properties.png)

1. V části **globálního správce může spravovat předplatná Azure a skupiny pro správu**, nastavení přepínače **Ano**.

   ![Globální správce může spravovat předplatná Azure a skupiny pro správu – snímek obrazovky](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Pokud nastavíte přepínač na **Ano**, váš účet globálního správce (aktuálně přihlášeného uživatele) je přidán do role správce přístupu uživatelů v Azure RBAC v kořenovém oboru (`/`), který uděluje přístup k zobrazení a sestavy na Všechna předplatná Azure přidružená klientovi Azure AD.

   Pokud nastavíte přepínač na **ne**, váš účet globálního správce (aktuálně přihlášeného uživatele) je odebrán z role správce přístupu uživatelů v Azure RBAC. Nelze zobrazit všechny odběry služby Azure přidružených ke klientovi Azure AD, a můžete zobrazit a spravovat pouze předplatná Azure ke kterým máte přístup.

1. Klikněte na tlačítko **Uložit** uložit vaše nastavení.

   Toto nastavení není globální vlastnost a platí pouze pro aktuálně přihlášeného uživatele.

1. Provedení úlohy, které musíte udělat na přístup se zvýšeným oprávněním. Až skončíte, nastavení přepínače zpět na **ne**.

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>Seznam přiřazení role v kořenovém oboru (/) pomocí prostředí PowerShell

Seznam přiřazení role správce přístupu uživatelů pro uživatele v kořenovém oboru (`/`), použijte [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) příkaz.

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

## <a name="delete-a-role-assignment-at-the-root-scope--using-powershell"></a>Umožňuje odstranit přiřazení role v kořenovém oboru (/) pomocí prostředí PowerShell

Chcete-li odstranit přiřazení role správce přístupu uživatelů pro uživatele v kořenovém oboru (`/`), použijte [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) příkaz.

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>Zvýšení oprávnění přístupu pro globálního správce pomocí rozhraní REST API

Použijte následující základní kroky ke zvýšení oprávnění přístupu pro globálního správce pomocí rozhraní REST API.

1. Pomocí REST, volání `elevateAccess`, která vám uděluje roli správce přístupu uživatelů v kořenovém oboru (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Vytvoření [přiřazení role](/rest/api/authorization/roleassignments) přiřadit žádnou roli v jakémkoli oboru. Následující příklad ukazuje vlastnosti přiřazení role {hodnoty vlastnosti roleDefinitionID} v kořenovém oboru (`/`):

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

1. Při správce přístupu uživatelů, můžete také odstranit přiřazení role v kořenovém oboru (`/`).

1. Vaše oprávnění správce přístupu uživatelů odvolat, dokud se znovu požadován.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>Vrácení zpět elevateAccess akce s rozhraní REST API

Při volání `elevateAccess`, vytvořit přiřazení role pro sebe, takže k odvolání těchto oprávnění byste měli odstranit přiřazení.

1. Volání [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) kde `roleName` rovná správce přístupu uživatelů k určení ID názvu role uživatele přístup správce.

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

    Uložit ID z `name` parametr v tomto případě `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. Musíte také seznam přiřazení role pro správce klienta na obor klienta. Seznam na obor klienta pro všechna přiřazení `principalId` správce klienta, který provedené přístup zvýšení volání. To se zobrazí seznam všech přiřazení v klientovi pro objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Správce klienta by neměl mít mnoho přiřazení, pokud předchozí dotaz vrátí příliš mnoho přiřazení, se můžete dotazovat i pro všechna přiřazení právě na úrovni oboru klienta a pak filtrovat výsledky: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Předchozí volání vrátí seznam přiřazení rolí. Najít přiřazení role, kde je oboru "/" a `roleDefinitionId` končí ID název role, který jste získali v kroku 1 a `principalId` odpovídá objectId správce klienta. 
    
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
        
    Znovu uložit ID z `name` parametr, v takovém případě e7dd75bc-06f6-4e71-9014-ee96a929d099.

    3. Nakonec pomocí ID přiřazení role můžete odstranit přiřazení přidal `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Další postup

- [Řízení přístupu na základě rolí pomocí REST](role-assignments-rest.md)
- [Spravovat přístup k přiřazení](role-assignments-users.md)
