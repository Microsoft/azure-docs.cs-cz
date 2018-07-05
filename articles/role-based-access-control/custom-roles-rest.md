---
title: Vytvoření vlastní role pomocí rozhraní REST API – Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit vlastní role řízení přístupu na základě role (RBAC) pomocí rozhraní REST API. Jedná se o seznam, vytvářet, aktualizovat a odstraňovat vlastní role.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8a1bbe8217e2d4a9846f56124e248e19cbe70b19
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436058"
---
# <a name="create-custom-roles-using-the-rest-api"></a>Vytvoření vlastní role pomocí rozhraní REST API

Pokud [předdefinované role](built-in-roles.md) nesplňují konkrétní požadavky vaší organizace, můžete vytvořit své vlastní role. Tento článek popisuje, jak vytvořit a spravovat vlastní role pomocí rozhraní REST API.

## <a name="list-roles"></a>Výpis rolí

Chcete-li vypsat všechny role nebo získat informace o jedné role pomocí názvu zobrazení, použijte [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) rozhraní REST API. K volání tohoto rozhraní API, musíte mít přístup k `Microsoft.Authorization/roleDefinitions/read` operace v oboru. Několik [předdefinované role](built-in-roles.md) je udělen přístup k této operace.

1. Začněte s následující žádosti:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s rozsahem, pro které chcete seznam rolí.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{filter}* splňující podmínku, kterou chcete použít k filtrování seznamu rolí.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | Seznam rolí, které jsou k dispozici pro přiřazení v zadaném rozsahu a všechny jeho podřízené obory. |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Kódování URL formulář přesné zobrazovaného názvu role. Pro instanci `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="get-information-about-a-role"></a>Získání informací o roli

Pokud chcete získat informace o roli pomocí jeho identifikátoru definice role, použít [definice rolí – získání](/rest/api/authorization/roledefinitions/get) rozhraní REST API. K volání tohoto rozhraní API, musíte mít přístup k `Microsoft.Authorization/roleDefinitions/read` operace v oboru. Několik [předdefinované role](built-in-roles.md) je udělen přístup k této operace.

Pokud chcete získat informace o jedné role pomocí názvu zobrazení, najdete v předchozí [seznamu rolí](custom-roles-rest.md#list-roles) oddílu.

1. Použití [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) rozhraní REST API pro získání identifikátoru GUID pro roli. Předdefinované role, můžete také získat identifikátor z [předdefinované role](built-in-roles.md).

1. Začněte s následující žádosti:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s rozsahem, pro které chcete seznam rolí.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* s identifikátorem GUID definice role.

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Chcete-li vytvořit vlastní roli, použijte [definice rolí – vytvořit nebo aktualizovat](/rest/api/authorization/roledefinitions/createorupdate) rozhraní REST API. K volání tohoto rozhraní API, musíte mít přístup k `Microsoft.Authorization/roleDefinitions/write` všechny operace `assignableScopes`. Předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) je udělen přístup k této operace. 

1. Projděte si seznam [operace poskytovatele prostředků](resource-provider-operations.md) , které jsou k dispozici se vytvořit oprávnění pro vlastní roli.

1. Generovat jedinečný identifikátor, který se použije pro identifikátor vlastní role pomocí nástroje identifikátor GUID. Tento identifikátor má následující formát: `00000000-0000-0000-0000-000000000000`

1. Začněte s následující žádosti a text:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s prvním `assignableScopes` vlastní role.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* s identifikátorem GUID vlastní role.

1. V těle žádosti v `assignableScopes` vlastnost, nahraďte *{roleDefinitionId}* s identifikátorem GUID.

1. Nahraďte *{subscriptionId}* identifikátorem předplatného.

1. V `actions` vlastnost, přidejte operace, které umožňuje provádět roli.

1. V `notActions` vlastnost, přidejte operace, které jsou vyloučené z povolených `actions`.

1. V `roleName` a `description` vlastnosti, zadejte jedinečný název a popis. Další informace o vlastnostech najdete v tématu [vlastní role](custom-roles.md).

    Následuje příklad těla žádosti:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

Chcete-li aktualizovat vlastní roli, použijte [definice rolí – vytvořit nebo aktualizovat](/rest/api/authorization/roledefinitions/createorupdate) rozhraní REST API. K volání tohoto rozhraní API, musíte mít přístup k `Microsoft.Authorization/roleDefinitions/write` všechny operace `assignableScopes`. Předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) je udělen přístup k této operace. 

1. Použití [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) nebo [definice rolí – získání](/rest/api/authorization/roledefinitions/get) rozhraní REST API a získat informace o vlastní roli. Další informace najdete v tématu dříve [seznamu rolí](custom-roles-rest.md#list-roles) oddílu.

1. Začněte s následující žádosti:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s prvním `assignableScopes` vlastní role.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* s identifikátorem GUID vlastní role.

1. Text požadavku na základě informací o vlastní roli, vytvořte v následujícím formátu:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Aktualizace textu žádosti se změnami, které chcete, aby pro vlastní roli.

    Následuje příklad těla žádosti v s byla přidána nová akce nastavení diagnostiky:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Odstranění vlastní role

Pokud chcete odstranit vlastní roli, použijte [odstranit definice rolí –](/rest/api/authorization/roledefinitions/delete) rozhraní REST API. K volání tohoto rozhraní API, musíte mít přístup k `Microsoft.Authorization/roleDefinitions/delete` všechny operace `assignableScopes`. Předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) je udělen přístup k této operace. 

1. Použití [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) nebo [definice rolí – získání](/rest/api/authorization/roledefinitions/get) rozhraní REST API pro získání identifikátoru GUID vlastní role. Další informace najdete v tématu dříve [seznamu rolí](custom-roles-rest.md#list-roles) oddílu.

1. Začněte s následující žádosti:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s rozsahem, který chcete odstranit vlastní roli.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* s identifikátorem GUID vlastní role.

## <a name="next-steps"></a>Další postup

- [Vlastní role v Azure](custom-roles.md)
- [Správa přístupu pomocí RBAC a rozhraní REST API](role-assignments-rest.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)