---
title: Vytvoření nebo aktualizace vlastních rolí pro prostředky Azure pomocí REST API
description: Přečtěte si, jak vypsat, vytvořit, aktualizovat nebo odstranit vlastní role pomocí řízení přístupu na základě role (RBAC) pro prostředky Azure pomocí REST API.
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
ms.openlocfilehash: 145bc45e1b7faeddc23cf5f0662337e15ab51c29
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137364"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Vytvoření nebo aktualizace vlastních rolí pro prostředky Azure pomocí REST API

Pokud [předdefinované role pro prostředky Azure](built-in-roles.md) nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní role. Tento článek popisuje, jak pomocí REST API vypsat, vytvořit, aktualizovat nebo odstranit vlastní role.

## <a name="list-custom-roles"></a>Výpis vlastních rolí

K vypsání všech vlastních rolí v adresáři použijte REST API [Definice rolí – seznam](/rest/api/authorization/roledefinitions/list) .

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Nahraďte *{Filter}* typem role.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrovat podle typu CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Vypsání vlastních rolí v oboru

K vypsání vlastních rolí v oboru použijte REST API [Definice rolí – seznam](/rest/api/authorization/roledefinitions/list) .

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam rolí.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{Filter}* typem role.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrovat podle typu CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Výpis definice vlastní role podle názvu

Pokud chcete získat informace o vlastní roli pomocí jejího zobrazovaného názvu, použijte [Definice rolí – získat](/rest/api/authorization/roledefinitions/get) REST API.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam rolí.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{Filter}* zobrazovaným názvem role.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Použijte kódovaný formát URL s přesným zobrazovaným názvem role. `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` například |

## <a name="list-a-custom-role-definition-by-id"></a>Výpis definice vlastní role podle ID

Pokud chcete získat informace o vlastní roli pomocí jejího jedinečného identifikátoru, použijte [Definice rolí – získat](/rest/api/authorization/roledefinitions/get) REST API.

1. Pro získání identifikátoru GUID pro roli použijte REST API [seznam definice rolí](/rest/api/authorization/roledefinitions/list) .

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam rolí.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* identifikátorem GUID definice role.

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Pokud chcete vytvořit vlastní roli, použijte [Definice rolí – vytvořit nebo aktualizovat](/rest/api/authorization/roledefinitions/createorupdate) REST API. Chcete-li volat toto rozhraní API, musíte být přihlášeni pomocí uživatele, kterému je přiřazena role s oprávněním `Microsoft.Authorization/roleDefinitions/write` pro všechny `assignableScopes`. Z předdefinovaných rolí zahrnuje toto oprávnění pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Projděte si seznam [operací poskytovatele prostředků](resource-provider-operations.md) , které jsou k dispozici pro vytvoření oprávnění pro vlastní roli.

1. Pomocí nástroje GUID vygenerujte jedinečný identifikátor, který se použije pro vlastní identifikátor role. Identifikátor má formát: `00000000-0000-0000-0000-000000000000`

1. Začněte s následujícím požadavkem a textem:

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

1. V rámci identifikátoru URI nahraďte *{Scope}* první `assignableScopes` vlastní role.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* identifikátorem GUID vlastní role.

1. V těle žádosti ve vlastnosti `assignableScopes` nahraďte *{roleDefinitionId}* identifikátorem GUID.

1. Nahraďte *{SubscriptionId}* identifikátorem vašeho předplatného.

1. Do vlastnosti `actions` přidejte operace, které může role provést.

1. Do vlastnosti `notActions` přidejte operace, které jsou vyloučeny z povolených `actions`.

1. Ve vlastnostech `roleName` a `description` zadejte jedinečný název role a popis. Další informace o vlastnostech naleznete v tématu [Custom Roles](custom-roles.md).

    V následujícím příkladu vidíte příklad textu žádosti:

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

Pokud chcete aktualizovat vlastní roli, použijte [Definice rolí – vytvořit nebo aktualizovat](/rest/api/authorization/roledefinitions/createorupdate) REST API. Chcete-li volat toto rozhraní API, musíte být přihlášeni pomocí uživatele, kterému je přiřazena role s oprávněním `Microsoft.Authorization/roleDefinitions/write` pro všechny `assignableScopes`. Z předdefinovaných rolí zahrnuje toto oprávnění pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Použijte definice [rolí – seznam](/rest/api/authorization/roledefinitions/list) nebo [definice rolí –](/rest/api/authorization/roledefinitions/get) REST API získat informace o vlastní roli. Další informace najdete v části věnované [vlastním rolím seznamu](#list-custom-roles) dříve.

1. Začněte s následujícím požadavkem:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* první `assignableScopes` vlastní role.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* identifikátorem GUID vlastní role.

1. Na základě informací o vlastní roli vytvořte text žádosti s následujícím formátem:

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

1. Aktualizujte text žádosti o změny, které chcete provést v rámci vlastní role.

    V následujícím příkladu vidíte příklad textu žádosti s přidanou novou akcí diagnostického nastavení:

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

Pokud chcete odstranit vlastní roli, použijte [Definice rolí – odstranit](/rest/api/authorization/roledefinitions/delete) REST API. Chcete-li volat toto rozhraní API, musíte být přihlášeni pomocí uživatele, kterému je přiřazena role s oprávněním `Microsoft.Authorization/roleDefinitions/delete` pro všechny `assignableScopes`. Z předdefinovaných rolí zahrnuje toto oprávnění pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Použijte definice [rolí – seznam](/rest/api/authorization/roledefinitions/list) nebo [Definice rolí – získat](/rest/api/authorization/roledefinitions/get) REST API pro získání identifikátoru GUID vlastní role. Další informace najdete v části věnované [vlastním rolím seznamu](#list-custom-roles) dříve.

1. Začněte s následujícím požadavkem:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, ve kterém chcete odstranit vlastní roli.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* identifikátorem GUID vlastní role.

## <a name="next-steps"></a>Další kroky

- [Vlastní role pro prostředky Azure](custom-roles.md)
- [Správa přístupu k prostředkům Azure pomocí RBAC a REST API](role-assignments-rest.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)
