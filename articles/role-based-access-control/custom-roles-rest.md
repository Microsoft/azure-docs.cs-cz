---
title: Vytvoření nebo aktualizace vlastních rolí Azure pomocí REST API – Azure RBAC
description: Přečtěte si, jak pomocí REST API a řízení přístupu na základě role Azure (Azure RBAC) získat informace o tom, jak vytvářet, aktualizovat nebo odstraňovat vlastní role Azure.
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
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ab64765fc4e329c8edbf7a4db813113d20d476b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557521"
---
# <a name="create-or-update-azure-custom-roles-using-the-rest-api"></a>Pomocí REST API vytvořit nebo aktualizovat vlastní role Azure

> [!IMPORTANT]
> Přidání skupiny pro správu do `AssignableScopes` je aktuálně ve verzi Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud [předdefinované role Azure](built-in-roles.md) nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní role. Tento článek popisuje, jak pomocí REST API vypsat, vytvořit, aktualizovat nebo odstranit vlastní role.

## <a name="list-custom-roles"></a>Výpis vlastních rolí

K vypsání všech vlastních rolí v adresáři použijte REST API [Definice rolí – seznam](/rest/api/authorization/roledefinitions/list) .

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Nahraďte *{Filter}* typem role.

    > [!div class="mx-tableFixed"]
    > | Filtrovat | Description |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtrovat podle typu CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Vypsání vlastních rolí v oboru

K vypsání vlastních rolí v oboru použijte REST API [Definice rolí – seznam](/rest/api/authorization/roledefinitions/list) .

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam rolí.

    > [!div class="mx-tableFixed"]
    > | Obor | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Prostředek |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |

1. Nahraďte *{Filter}* typem role.

    > [!div class="mx-tableFixed"]
    > | Filtrovat | Description |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtrovat podle typu CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Výpis definice vlastní role podle názvu

Pokud chcete získat informace o vlastní roli pomocí jejího zobrazovaného názvu, použijte [Definice rolí – získat](/rest/api/authorization/roledefinitions/get) REST API.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam rolí.

    > [!div class="mx-tableFixed"]
    > | Obor | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Prostředek |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |

1. Nahraďte *{Filter}* zobrazovaným názvem role.

    > [!div class="mx-tableFixed"]
    > | Filtrovat | Description |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Použijte kódovaný formát URL s přesným zobrazovaným názvem role. Například `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Výpis definice vlastní role podle ID

Pokud chcete získat informace o vlastní roli pomocí jejího jedinečného identifikátoru, použijte [Definice rolí – získat](/rest/api/authorization/roledefinitions/get) REST API.

1. Pro získání identifikátoru GUID pro roli použijte REST API [seznam definice rolí](/rest/api/authorization/roledefinitions/list) .

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam rolí.

    > [!div class="mx-tableFixed"]
    > | Obor | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Prostředek |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |

1. Nahraďte *{roleDefinitionId}* identifikátorem GUID definice role.

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Pokud chcete vytvořit vlastní roli, použijte [Definice rolí – vytvořit nebo aktualizovat](/rest/api/authorization/roledefinitions/createorupdate) REST API. Chcete-li volat toto rozhraní API, musíte být přihlášeni pomocí uživatele, kterému je přiřazena role s `Microsoft.Authorization/roleDefinitions/write` oprávněním pro všechny `assignableScopes` . Z předdefinovaných rolí zahrnuje toto oprávnění pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

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
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* první `assignableScopes` z vlastní role.

    > [!div class="mx-tableFixed"]
    > | Obor | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Skupina prostředků |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |

1. Nahraďte *{roleDefinitionId}* identifikátorem GUID vlastní role.

1. V těle žádosti nahraďte *{roleDefinitionId}* identifikátorem GUID.

1. Pokud `assignableScopes` se jedná o předplatné nebo skupinu prostředků, nahraďte své identifikátory instancemi *{SubscriptionId}* nebo *{resourceName}* .

1. Pokud `assignableScopes` je skupina pro správu, nahraďte instanci *{GroupID}* identifikátorem skupiny pro správu. Přidání skupiny pro správu do `assignableScopes` je aktuálně ve verzi Preview.

1. Do `actions` Vlastnosti přidejte operace, které může role provést.

1. Do `notActions` Vlastnosti přidejte operace, které jsou vyloučeny z povolených `actions` .

1. Ve `roleName` vlastnostech a `description` Zadejte jedinečný název role a popis. Další informace o vlastnostech najdete v tématu [vlastní role Azure](custom-roles.md).

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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

Pokud chcete aktualizovat vlastní roli, použijte [Definice rolí – vytvořit nebo aktualizovat](/rest/api/authorization/roledefinitions/createorupdate) REST API. Chcete-li volat toto rozhraní API, musíte být přihlášeni pomocí uživatele, kterému je přiřazena role s `Microsoft.Authorization/roleDefinitions/write` oprávněním pro všechny `assignableScopes` . Z předdefinovaných rolí zahrnuje toto oprávnění pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Použijte definice [rolí – seznam](/rest/api/authorization/roledefinitions/list) nebo [definice rolí –](/rest/api/authorization/roledefinitions/get) REST API získat informace o vlastní roli. Další informace najdete v části věnované [vlastním rolím seznamu](#list-custom-roles) dříve.

1. Začněte s následujícím požadavkem:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* první `assignableScopes` z vlastní role.

    > [!div class="mx-tableFixed"]
    > | Obor | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Skupina prostředků |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |

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
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Odstranění vlastní role

Pokud chcete odstranit vlastní roli, použijte [Definice rolí – odstranit](/rest/api/authorization/roledefinitions/delete) REST API. Chcete-li volat toto rozhraní API, musíte být přihlášeni pomocí uživatele, kterému je přiřazena role s `Microsoft.Authorization/roleDefinitions/delete` oprávněním pro všechny `assignableScopes` . Z předdefinovaných rolí zahrnuje toto oprávnění pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Použijte definice [rolí – seznam](/rest/api/authorization/roledefinitions/list) nebo [Definice rolí – získat](/rest/api/authorization/roledefinitions/get) REST API pro získání identifikátoru GUID vlastní role. Další informace najdete v části věnované [vlastním rolím seznamu](#list-custom-roles) dříve.

1. Začněte s následujícím požadavkem:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, ve kterém chcete odstranit vlastní roli.

    > [!div class="mx-tableFixed"]
    > | Obor | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Skupina prostředků |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |

1. Nahraďte *{roleDefinitionId}* identifikátorem GUID vlastní role.

## <a name="next-steps"></a>Další kroky

- [Vlastní role Azure](custom-roles.md)
- [Přiřazení rolí Azure pomocí REST API](role-assignments-rest.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)
