---
title: Přiřazení rolí správce Azure AD k rozhraní Microsoft Graph API | Microsoft Docs
description: Přiřazení a odebrání rolí správce Azure AD pomocí Graph API v Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67e77c2dd08386279beeb06ff9081fa2669afa73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102487830"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Přiřaďte vlastní role správců pomocí rozhraní Microsoft Graph API v Azure Active Directory 

Můžete automatizovat způsob přiřazování rolí uživatelským účtům pomocí rozhraní Microsoft Graph API. Tento článek popisuje operace POST, GET a DELETE na roleAssignments.

## <a name="required-permissions"></a>Požadovaná oprávnění

Připojte se k vaší organizaci Azure AD pomocí účtu správce globálního správce nebo privilegované role, abyste mohli role přiřadit nebo odebrat.

## <a name="post-operations-on-roleassignment"></a>Operace POST na RoleAssignment

### <a name="example-1-create-a-role-assignment-between-a-user-and-a-role-definition"></a>Příklad 1: vytvoření přiřazení role mezi uživatelem a definicí role

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json
```

Text

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "directoryScopeId":"/"  // Don't use "resourceScope" attribute in Azure AD role assignments. It will be deprecated soon.
}
```

Odpověď

``` HTTP
HTTP/1.1 201 Created
```

### <a name="example-2-create-a-role-assignment-where-the-principal-or-role-definition-does-not-exist"></a>Příklad 2: vytvoření přiřazení role, kde neexistuje definice objektu zabezpečení nebo role

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Text

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "directoryScopeId":"/"  //Don't use "resourceScope" attribute in Azure AD role assignments. It will be deprecated soon.
}
```

Odpověď

``` HTTP
HTTP/1.1 404 Not Found
```
### <a name="example-3-create-a-role-assignment-on-a-single-resource-scope"></a>Příklad 3: vytvoření přiřazení role v jednom oboru prostředků

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Text

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"e9b2b976-1dea-4229-a078-b08abd6c4f84",    //role template ID of a custom role
    "directoryScopeId":"/13ff0c50-18e7-4071-8b52-a6f08e17c8cc"  //object ID of an application
}
```

Odpověď

``` HTTP
HTTP/1.1 201 Created
```

### <a name="example-4-create-an-administrative-unit-scoped-role-assignment-on-a-built-in-role-definition-which-is-not-supported"></a>Příklad 4: vytvoření přiřazení role s oborem správy u předdefinované definice role, která není podporovaná

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Text

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"29232cdf-9323-42fd-ade2-1d097af3e4de",    //role template ID of Exchange Administrator
    "directoryScopeId":"/administrativeUnits/13ff0c50-18e7-4071-8b52-a6f08e17c8cc"    //object ID of an administrative unit
}
```

Odpověď

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "message":"The given built-in role is not supported to be assigned to a single resource scope."
        }
    }
}
```

Pro rozsah jednotek správy je povolena pouze podmnožina předdefinovaných rolí. V [této dokumentaci](admin-units-assign-roles.md) najdete seznam integrovaných rolí podporovaných prostřednictvím jednotky pro správu.

## <a name="get-operations-on-roleassignment"></a>ZÍSKAT operace na RoleAssignment

### <a name="example-5-get-role-assignments-for-a-given-principal"></a>Příklad 5: získání přiřazení rolí pro daný objekt zabezpečení

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId+eq+'<object-id-of-principal>'
```

Odpověď

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            { 
                "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
                "directoryScopeId":"/"  
            } ,
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"fe930be7-5e62-47db-91af-98c3a49a38b1",
                "directoryScopeId":"/"
            }
        ]
}
```

### <a name="example-6-get-role-assignments-for-a-given-role-definition"></a>Příklad 6: získání přiřazení rolí pro danou definici role

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=roleDefinitionId+eq+'<object-id-or-template-id-of-role-definition>'
```

Odpověď

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"fe930be7-5e62-47db-91af-98c3a49a38b1",
                "directoryScopeId":"/"
            }
     ]
}
```

### <a name="example-7-get-a-role-assignment-by-id"></a>Příklad 7: získání přiřazení role podle ID

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpověď

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "directoryScopeId":"/"
}
```

### <a name="example-8-get-role-assignments-for-a-given-scope"></a>Příklad 8: získání přiřazení rolí pro daný obor


GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=directoryScopeId+eq+'/d23998b1-8853-4c87-b95f-be97d6c6b610'
```

Odpověď

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            { 
                "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
                "directoryScopeId":"/d23998b1-8853-4c87-b95f-be97d6c6b610"
            } ,
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
                "directoryScopeId":"/d23998b1-8853-4c87-b95f-be97d6c6b610"
            }
        ]
}
```

## <a name="delete-operations-on-roleassignment"></a>ODSTRANĚNÍ operací na RoleAssignment

### <a name="example-9-delete-a-role-assignment-between-a-user-and-a-role-definition"></a>Příklad 9: odstranění přiřazení rolí mezi uživatelem a definicí role

DELETE

``` HTTP
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpověď
``` HTTP
HTTP/1.1 204 No Content
```

### <a name="example-10-delete-a-role-assignment-that-no-longer-exists"></a>Příklad 10: odstranění přiřazení role, které už neexistuje

DELETE

``` HTTP
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpověď

``` HTTP
HTTP/1.1 404 Not Found
```

### <a name="example-11-delete-a-role-assignment-between-self-and-global-administrator-role-definition"></a>Příklad 11: odstranění přiřazení role mezi definicí role sebe a globálního správce

DELETE

``` HTTP
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpověď

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Removing self from Global Administrator built-in role is not allowed"},
            "values":null
        }
    }
}
```

Uživatelům bráníme v odstranění vlastní globální role správce, aby se předešlo situaci, kdy má tenant nula globálních správců. Odebrání jiných rolí přiřazených k sobě je povoleno.

## <a name="next-steps"></a>Další kroky

* Sdílejte s námi na [fóru role pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) .
* Další informace o rolích a přiřazení rolí správců najdete v tématu [přiřazení rolí správce](permissions-reference.md) .
* Výchozí uživatelská oprávnění najdete v tématu [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md) .