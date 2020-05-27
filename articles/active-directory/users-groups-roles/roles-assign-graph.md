---
title: Přiřazení rolí správce Azure AD k rozhraní Microsoft Graph API | Microsoft Docs
description: Přiřazení a odebrání rolí správce Azure AD pomocí Graph API v Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ac6f06a936893f26c634951e50a3579401c15d5
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869606"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Přiřaďte vlastní role správců pomocí rozhraní Microsoft Graph API v Azure Active Directory 

Můžete automatizovat způsob přiřazování rolí uživatelským účtům pomocí rozhraní Microsoft Graph API. Tento článek popisuje operace POST, GET a DELETE na roleAssignments.

## <a name="required-permissions"></a>Požadovaná oprávnění

Připojte se ke svojí organizaci Azure AD pomocí účtu globálního správce nebo správce privilegovaných identit k přiřazení nebo odebrání rolí.

## <a name="post-operations-on-roleassignment"></a>Operace POST na RoleAssignment

Požadavek HTTP na vytvoření přiřazení role mezi uživatelem a definicí role

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
    "resourceScopes":"/"
}
```

Odpověď

``` HTTP
HTTP/1.1 201 Created
```

Požadavek HTTP na vytvoření přiřazení role, kde neexistuje definice objektu zabezpečení nebo role

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Text

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/"
}
```

Odpověď

``` HTTP
HTTP/1.1 404 Not Found
```

Požadavek HTTP na vytvoření jednoho přiřazení role v oboru prostředků u předdefinované definice role.

> [!NOTE] 
> Předdefinované role v současnosti mají omezení, kde můžou být vymezené jenom na rozsah "/" v rámci organizace nebo na obor "/AU/*". Obory jednoho prostředku nefungují pro předdefinované role, ale fungují pro vlastní role.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Text

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
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
            "lang":"en",
            "value":"Provided authorization scope is not supported for built-in role definitions."},
            "values":
            [
                {
                    "item":"scope",
                    "value":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
                }
            ]
        }
    }
}
```

## <a name="get-operations-on-roleassignment"></a>ZÍSKAT operace na RoleAssignment

Požadavek HTTP na získání přiřazení role pro daný objekt zabezpečení

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=principalId eq ‘<object-id-of-principal>’
```

Odpověď

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":"/"
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":"/"
}
```

Požadavek HTTP na získání přiřazení role pro danou definici role

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Odpověď

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":"/"
}
```

Požadavek HTTP na získání přiřazení role podle ID

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
    "resourceScopes":"/"
}
```

## <a name="delete-operations-on-roleassignment"></a>ODSTRANĚNÍ operací na RoleAssignment

Požadavek HTTP na odstranění přiřazení role mezi uživatelem a definicí role

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpověď
``` HTTP
HTTP/1.1 204 No Content
```

Požadavek HTTP na odstranění přiřazení role, která už neexistuje

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpověď

``` HTTP
HTTP/1.1 404 Not Found
```

Požadavek HTTP na odstranění přiřazení role mezi samoobslužnou a vestavěnou definicí role

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
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
            "value":"Cannot remove self from built-in role definitions."},
            "values":null
        }
    }
}
```

## <a name="next-steps"></a>Další kroky

* Můžete s námi sdílet na [fóru role pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Další informace o rolích a přiřazení rolí správců najdete v tématu [přiřazení rolí správce](directory-assign-admin-roles.md).
* Pro výchozí uživatelská oprávnění si přečtěte téma [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md).
