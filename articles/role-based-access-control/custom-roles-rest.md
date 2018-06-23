---
title: Vytvoření vlastních rolí pomocí rozhraní REST API – Azure | Microsoft Docs
description: Naučte se vytvářet vlastní role pro řízení přístupu na základě role (RBAC) pomocí rozhraní REST API. To zahrnuje seznam, vytvářet, aktualizovat a odstraňovat vlastní role.
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
ms.topic: article
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8267846fed30baf2c37dcddd453ae9ead9341da9
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320587"
---
# <a name="create-custom-roles-using-the-rest-api"></a>Vytvoření vlastních rolí pomocí rozhraní REST API

Pokud [předdefinované role](built-in-roles.md) nemáte splnili specifické požadavky vaší organizace, můžete vytvořit vlastní role. Tento článek popisuje, jak vytvářet a spravovat vlastní role pomocí rozhraní REST API.

## <a name="list-roles"></a>Seznam rolí

Zobrazí seznam všech rolí nebo získat informace o jedné role pomocí názvu zobrazení, použijte [definice rolí - seznamu](/rest/api/authorization/roledefinitions/list) REST API. Toto rozhraní API volat, musí mít přístup k `Microsoft.Authorization/roleDefinitions/read` operace v oboru. Několik [předdefinované role](built-in-roles.md) mají udělen přístup k této operaci.

1. Můžete začít s následující požadavek:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI, nahraďte *{oboru}* s oborem, pro které chcete zobrazit seznam rolí.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{filtru}* s podmínku, kterou chcete použít pro filtrování seznamu role.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | Zobrazí seznam rolí, které jsou k dispozici pro přiřazení v zadaném oboru a všechny její podřízené obory. |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Pomocí formuláře kódovaná adresou URL přesný zobrazovaného názvu role. Pro instanci, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="get-information-about-a-role"></a>Získat informace o roli

Chcete-li získat informace o roli pomocí jeho identifikátoru definice role, použijte [získat definice rolí -](/rest/api/authorization/roledefinitions/get) REST API. Toto rozhraní API volat, musí mít přístup k `Microsoft.Authorization/roleDefinitions/read` operace v oboru. Několik [předdefinované role](built-in-roles.md) mají udělen přístup k této operaci.

Chcete-li získat informace o jedné role pomocí názvu zobrazení, najdete v předchozí [seznam rolí](custom-roles-rest.md#list-roles) části.

1. Použití [definice rolí - seznamu](/rest/api/authorization/roledefinitions/list) REST API pro získání identifikátoru GUID pro roli. Pro předdefinované role, můžete také získat identifikátor z [předdefinované role](built-in-roles.md).

1. Můžete začít s následující požadavek:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI, nahraďte *{oboru}* s oborem, pro které chcete zobrazit seznam rolí.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{hodnoty vlastnosti roleDefinitionId}* s identifikátorem GUID definice role.

## <a name="create-a-custom-role"></a>Vytvořit vlastní roli

Chcete-li vytvořit vlastní roli, použijte [definice rolí - vytvořit nebo aktualizovat](/rest/api/authorization/roledefinitions/createorupdate) REST API. Toto rozhraní API volat, musí mít přístup k `Microsoft.Authorization/roleDefinitions/write` operaci na všech `assignableScopes`. Z předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce přístupu uživatelů](built-in-roles.md#user-access-administrator) mají udělen přístup k této operaci. 

1. Projděte si seznam [operace poskytovatele prostředků](resource-provider-operations.md) které jsou k dispozici pro vytvoření oprávnění pro vaše vlastní role.

1. Generovat jedinečný identifikátor, který se použije pro identifikátor vlastní role pomocí nástroje identifikátor GUID. Identifikátor má formát: `00000000-0000-0000-0000-000000000000`

1. Můžete začít s následující žádosti a text:

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

1. V rámci identifikátoru URI, nahraďte *{oboru}* s prvním `assignableScopes` vlastní role.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{hodnoty vlastnosti roleDefinitionId}* s identifikátorem GUID vlastní role.

1. V těle žádosti v `assignableScopes` vlastnost, nahraďte *{hodnoty vlastnosti roleDefinitionId}* s identifikátorem GUID.

1. Nahraďte *{subscriptionId}* s ID vašeho předplatného.

1. V `actions` vlastnost, přidejte operace, které umožňuje provést roli.

1. V `notActions` vlastnost, přidání operací, které budou vyloučeny z povolené maximum `actions`.

1. V `roleName` a `description` vlastnosti, zadejte jedinečný název a popis. Další informace o vlastnostech najdete v tématu [vlastní role](custom-roles.md).

    Na obrázku je příkladem obsah žádosti:

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

## <a name="update-a-custom-role"></a>Aktualizovat vlastní role

Chcete-li aktualizovat vlastní roli, použijte [definice rolí - vytvořit nebo aktualizovat](/rest/api/authorization/roledefinitions/createorupdate) REST API. Toto rozhraní API volat, musí mít přístup k `Microsoft.Authorization/roleDefinitions/write` operaci na všech `assignableScopes`. Z předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce přístupu uživatelů](built-in-roles.md#user-access-administrator) mají udělen přístup k této operaci. 

1. Použití [definice rolí - seznamu](/rest/api/authorization/roledefinitions/list) nebo [získat definice rolí -](/rest/api/authorization/roledefinitions/get) REST API a získat informace o vlastních rolí. Další informace najdete v tématu dříve [seznam rolí](custom-roles-rest.md#list-roles) části.

1. Můžete začít s následující požadavek:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI, nahraďte *{oboru}* s prvním `assignableScopes` vlastní role.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{hodnoty vlastnosti roleDefinitionId}* s identifikátorem GUID vlastní role.

1. Obsah žádosti na základě informací o vlastní roli, vytvořte v následujícím formátu:

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

1. Aktualizace textu žádosti se změnami, které chcete provádět vlastní role.

    Následující ukazuje příklad textu žádosti pomocí nového nastavení pro diagnostiku akce přidán:

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

## <a name="delete-a-custom-role"></a>Odstranit vlastní roli

Chcete-li odstranit vlastní roli, použijte [odstranit definice rolí -](/rest/api/authorization/roledefinitions/delete) REST API. Toto rozhraní API volat, musí mít přístup k `Microsoft.Authorization/roleDefinitions/delete` operaci na všech `assignableScopes`. Z předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce přístupu uživatelů](built-in-roles.md#user-access-administrator) mají udělen přístup k této operaci. 

1. Použití [definice rolí - seznamu](/rest/api/authorization/roledefinitions/list) nebo [získat definice rolí -](/rest/api/authorization/roledefinitions/get) REST API pro získání identifikátoru GUID vlastní role. Další informace najdete v tématu dříve [seznam rolí](custom-roles-rest.md#list-roles) části.

1. Můžete začít s následující požadavek:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI, nahraďte *{oboru}* rozsahu, který chcete odstranit vlastní roli.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{hodnoty vlastnosti roleDefinitionId}* s identifikátorem GUID vlastní role.

## <a name="next-steps"></a>Další postup

- [Vlastní role v Azure](custom-roles.md)
- [Správa přístupu pomocí RBAC a rozhraní REST API](role-assignments-rest.md)
- [Referenční dokumentace rozhraní API Azure REST](/rest/api/azure/)