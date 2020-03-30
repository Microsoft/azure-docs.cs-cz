---
title: Přiřazení rolí správce Azure AD pomocí rozhraní Microsoft Graph API | Dokumenty společnosti Microsoft
description: Přiřazení a odebrání rolí správce Azure AD pomocí rozhraní Graph API ve službě Azure AD
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3632f8a360df8837569104232b7380fdc8383953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559143"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Přiřazení rolí vlastních správců pomocí rozhraní Microsoft Graph API ve službě Azure Active Directory 

Pomocí rozhraní Microsoft Graph API můžete automatizovat způsob přiřazování rolí k uživatelským účtům. Tento článek popisuje operace POST, GET a DELETE na roleAssignments.

## <a name="required-permissions"></a>Požadovaná oprávnění

Připojte se ke svému tenantovi Azure AD pomocí globálního účtu správce nebo správce privilegované identity k přiřazení nebo odebrání rolí.

## <a name="post-operations-on-roleassignment"></a>Post operace na rolepřiřazení

Požadavek HTTP pro vytvoření přiřazení role mezi uživatelem a definicí role.

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json
```

Tělo

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

Odpověď

``` HTTP
HTTP/1.1 201 Created
```

Požadavek HTTP pro vytvoření přiřazení role, kde neexistuje definice jistiny nebo role

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Tělo

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

Odpověď

``` HTTP
HTTP/1.1 404 Not Found
```

Požadavek HTTP pro vytvoření přiřazení role s rozsahem jednoho prostředku pro předdefinovanou definici role.

> [!NOTE] 
> Předdefinované role dnes mají omezení, kde mohou být vymezeny pouze na rozsah celé organizace nebo "/AU/*" oboru. Obor jednoho prostředku nefunguje pro předdefinované role, ale funguje pro vlastní role.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Tělo

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/ab2e1023-bddc-4038-9ac1-ad4843e7e539"]
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

## <a name="get-operations-on-roleassignment"></a>GET Operace na přiřazení role

Požadavek HTTP pro získání přiřazení role pro danou hlavní moc

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
    "resourceScopes":["/"]
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

Požadavek HTTP pro získání přiřazení role pro danou definici role.

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
    "resourceScopes":["/"]
}
```

Požadavek HTTP pro získání přiřazení role podle ID.

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
    "resourceScopes":["/"]
}
```

## <a name="delete-operations-on-roleassignment"></a>Odstranit operace přiřazení role

Požadavek HTTP na odstranění přiřazení role mezi uživatelem a definicí role.

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpověď
``` HTTP
HTTP/1.1 204 No Content
```

Požadavek HTTP na odstranění přiřazení role, které již neexistuje

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpověď

``` HTTP
HTTP/1.1 404 Not Found
```

Požadavek HTTP na odstranění přiřazení role mezi vlastní a předdefinovanou definicí role

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

* Neváhejte se s námi podělit na [fóru rolí azure ad pro správu](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Další informace o rolích a přiřazení rolí správce najdete v tématu [Přiřazení rolí správce](directory-assign-admin-roles.md).
* Výchozí uživatelská oprávnění naleznete v [porovnání výchozích oprávnění hosta a člena uživatele](../fundamentals/users-default-permissions.md).
