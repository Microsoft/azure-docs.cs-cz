---
title: Vytvoření vlastních rolí pro prostředky Azure pomocí rozhraní REST API – Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet vlastní role pomocí řízení přístupu na základě rolí (RBAC) pro prostředky Azure pomocí rozhraní REST API. Jedná se o seznam, vytvářet, aktualizovat a odstraňovat vlastní role.
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
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4024f6fdb40c752ef61f348d15f681e81d81c08c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596678"
---
# <a name="create-custom-roles-for-azure-resources-using-the-rest-api"></a>Vytvoření vlastních rolí pro prostředky Azure pomocí rozhraní REST API

Pokud [předdefinované role pro prostředky Azure](built-in-roles.md) nesplňují konkrétním potřebám vaší organizace, můžete vytvořit vlastní role. Tento článek popisuje, jak vytvořit a spravovat vlastní role pomocí rozhraní REST API.

## <a name="list-custom-roles"></a>Výpis vlastních rolí

Chcete-li vypsat všechny vlastní role v adresáři, použijte [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) rozhraní REST API.

1. Začněte s následující žádosti:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Nahraďte *{filter}* s typem role.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrovat podle typu CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Seznam vlastních rolí v oboru

K zobrazení seznamu vlastních rolí v oboru, použijte [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) rozhraní REST API.

1. Začněte s následující žádosti:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s rozsahem, pro které chcete seznam rolí.

    | Rozsah | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{filter}* s typem role.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrovat podle typu CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Seznam definice vlastních rolí podle názvu

Chcete-li získat informace o vlastní role podle jeho zobrazovaného názvu, použijte [definice rolí – získání](/rest/api/authorization/roledefinitions/get) rozhraní REST API.

1. Začněte s následující žádosti:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s rozsahem, pro které chcete seznam rolí.

    | Rozsah | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{filter}* se zobrazovaným názvem pro roli.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Kódování URL formulář přesné zobrazovaného názvu role. Pro instanci `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Seznam definice vlastních rolí podle ID

Chcete-li získat informace o vlastní role podle jejího jedinečného identifikátoru, použijte [definice rolí – získání](/rest/api/authorization/roledefinitions/get) rozhraní REST API.

1. Použití [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) rozhraní REST API pro získání identifikátoru GUID pro roli.

1. Začněte s následující žádosti:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s rozsahem, pro které chcete seznam rolí.

    | Rozsah | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* s identifikátorem GUID definice role.

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Chcete-li vytvořit vlastní roli, použijte [definice rolí – vytvořit nebo aktualizovat](/rest/api/authorization/roledefinitions/createorupdate) rozhraní REST API. K volání tohoto rozhraní API, musíte být přihlášeni pomocí uživatele, který má přiřazenou roli, která má `Microsoft.Authorization/roleDefinitions/write` oprávnění ve všech `assignableScopes`. Předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) patří toto oprávnění.

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

    | Rozsah | Type |
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
              "Microsoft.ResourceHealth/availabilityStatuses/read",
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

Chcete-li aktualizovat vlastní roli, použijte [definice rolí – vytvořit nebo aktualizovat](/rest/api/authorization/roledefinitions/createorupdate) rozhraní REST API. K volání tohoto rozhraní API, musíte být přihlášeni pomocí uživatele, který má přiřazenou roli, která má `Microsoft.Authorization/roleDefinitions/write` oprávnění ve všech `assignableScopes`. Předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) patří toto oprávnění.

1. Použití [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) nebo [definice rolí – získání](/rest/api/authorization/roledefinitions/get) rozhraní REST API a získat informace o vlastní roli. Další informace najdete v tématu dříve [seznamu vlastních rolí](#list-custom-roles) oddílu.

1. Začněte s následující žádosti:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s prvním `assignableScopes` vlastní role.

    | Rozsah | Type |
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
              "Microsoft.ResourceHealth/availabilityStatuses/read",
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

Pokud chcete odstranit vlastní roli, použijte [odstranit definice rolí –](/rest/api/authorization/roledefinitions/delete) rozhraní REST API. K volání tohoto rozhraní API, musíte být přihlášeni pomocí uživatele, který má přiřazenou roli, která má `Microsoft.Authorization/roleDefinitions/delete` oprávnění ve všech `assignableScopes`. Předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) patří toto oprávnění.

1. Použití [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) nebo [definice rolí – získání](/rest/api/authorization/roledefinitions/get) rozhraní REST API pro získání identifikátoru GUID vlastní role. Další informace najdete v tématu dříve [seznamu vlastních rolí](#list-custom-roles) oddílu.

1. Začněte s následující žádosti:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s rozsahem, který chcete odstranit vlastní roli.

    | Rozsah | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* s identifikátorem GUID vlastní role.

## <a name="next-steps"></a>Další postup

- [Vlastní role pro prostředky Azure](custom-roles.md)
- [Správa přístupu k prostředkům Azure pomocí RBAC a rozhraní REST API](role-assignments-rest.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)