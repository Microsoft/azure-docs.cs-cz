---
title: Vytvoření nebo aktualizace vlastních rolí pro prostředky Azure pomocí rozhraní REST API
description: Zjistěte, jak vypsat, vytvořit, aktualizovat nebo odstranit vlastní role pomocí řízení přístupu na základě rolí (RBAC) pro prostředky Azure pomocí rozhraní REST API.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: fda0400310f46da64322654c42af75521746d679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062194"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Vytvoření nebo aktualizace vlastních rolí pro prostředky Azure pomocí rozhraní REST API

> [!IMPORTANT]
> Přidání skupiny `AssignableScopes` pro správu do aplikace je aktuálně ve verzi Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud [předdefinované role pro prostředky Azure](built-in-roles.md) nesplňují specifické potřeby vaší organizace, můžete si vytvořit vlastní role. Tento článek popisuje, jak seznam, vytvoření, aktualizaci nebo odstranění vlastních rolí pomocí rozhraní REST API.

## <a name="list-custom-roles"></a>Výpis vlastních rolí

Chcete-li vypsat všechny vlastní role v adresáři, použijte [definice rolí - seznam](/rest/api/authorization/roledefinitions/list) rozhraní REST API.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Nahraďte *{filter}* typem role.

    > [!div class="mx-tableFixed"]
    > | Filtr | Popis |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtr na základě typu CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Vypsat vlastní role v oboru

Chcete-li vypsat vlastní role v oboru, použijte [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) rozhraní REST API.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahraďte *{scope}* oborem, pro který chcete vypsat role.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Prostředek |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |

1. Nahraďte *{filter}* typem role.

    > [!div class="mx-tableFixed"]
    > | Filtr | Popis |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtr na základě typu CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Seznam vlastní definice role podle názvu

Chcete-li získat informace o vlastní roli podle jejího zobrazovaný název, použijte [definice rolí – získat](/rest/api/authorization/roledefinitions/get) rozhraní REST API.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahraďte *{scope}* oborem, pro který chcete vypsat role.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Prostředek |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |

1. Nahraďte *{filter}* zobrazovaným názvem role.

    > [!div class="mx-tableFixed"]
    > | Filtr | Popis |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Použijte formulář kódované adresou URL přesného zobrazovaného názvu role. Například`$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Vypsat vlastní definici role podle ID

Chcete-li získat informace o vlastní roli podle jeho jedinečný identifikátor, použijte [definice rolí – získat](/rest/api/authorization/roledefinitions/get) rozhraní REST API.

1. Pomocí [rozhraní ROLE - seznam](/rest/api/authorization/roledefinitions/list) rozhraní REST API získáte identifikátor GUID pro roli.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{scope}* oborem, pro který chcete vypsat role.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Prostředek |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |

1. Nahraďte *{roleDefinitionId}* identifikátorem IDENTIFIKÁTOR GUID definice role.

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Chcete-li vytvořit vlastní roli, použijte [definice rolí - vytvořit nebo aktualizovat](/rest/api/authorization/roledefinitions/createorupdate) rozhraní REST API. Chcete-li volat toto rozhraní API, musíte být přihlášeni `Microsoft.Authorization/roleDefinitions/write` k uživateli, kterému je přiřazena role, která má oprávnění ke všem rozhraním `assignableScopes`. Z předdefinovaných rolí toto oprávnění zahrnuje pouze [vlastník](built-in-roles.md#owner) a [správce přístupu uživatelů.](built-in-roles.md#user-access-administrator)

1. Zkontrolujte seznam [operací zprostředkovatele prostředků,](resource-provider-operations.md) které jsou k dispozici k vytvoření oprávnění pro vlastní roli.

1. Pomocí nástroje GUID vygenerujte jedinečný identifikátor, který bude použit pro vlastní identifikátor role. Identifikátor má formát:`00000000-0000-0000-0000-000000000000`

1. Začněte s následujícím požadavkem a tělem:

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

1. V rámci identifikátoru URI nahraďte *{scope}* první `assignableScopes` vlastní rolí.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Skupina prostředků |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |

1. Nahraďte *{roleDefinitionId}* identifikátorem identifikátoru GUID vlastní role.

1. V těle požadavku nahraďte *{roleDefinitionId}* identifikátorem IDENTIFIKÁTOR GUID.

1. Pokud `assignableScopes` je předplatné nebo skupina prostředků, nahraďte instance *{subscriptionId}* nebo *{resourceGroup}* identifikátory.

1. Pokud `assignableScopes` je skupina pro správu, nahraďte instanci *{groupId}* identifikátorem skupiny pro správu. Přidání skupiny `assignableScopes` pro správu do aplikace je aktuálně ve verzi Preview.

1. Ve `actions` vlastnosti přidejte operace, které umožňuje role provádět.

1. Ve `notActions` vlastnosti přidejte operace, které jsou `actions`vyloučeny z povolené .

1. Ve `roleName` vlastnostech a `description` zadejte jedinečný název role a popis. Další informace o vlastnostech naleznete v tématu [Vlastní role](custom-roles.md).

    Následující příklad těla požadavku:

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

Chcete-li aktualizovat vlastní roli, použijte [definice rolí – vytvořit nebo aktualizovat](/rest/api/authorization/roledefinitions/createorupdate) rozhraní REST API. Chcete-li volat toto rozhraní API, musíte být přihlášeni `Microsoft.Authorization/roleDefinitions/write` k uživateli, kterému je přiřazena role, která má oprávnění ke všem rozhraním `assignableScopes`. Z předdefinovaných rolí toto oprávnění zahrnuje pouze [vlastník](built-in-roles.md#owner) a [správce přístupu uživatelů.](built-in-roles.md#user-access-administrator)

1. Pomocí [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) nebo [definice rolí – získejte](/rest/api/authorization/roledefinitions/get) rozhraní REST API k získání informací o vlastní roli. Další informace naleznete v části dřívější [seznam vlastních rolí.](#list-custom-roles)

1. Začněte s následujícím požadavkem:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{scope}* první `assignableScopes` vlastní rolí.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Skupina prostředků |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |

1. Nahraďte *{roleDefinitionId}* identifikátorem identifikátoru GUID vlastní role.

1. Na základě informací o vlastní roli vytvořte tělo požadavku v následujícím formátu:

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

1. Aktualizujte tělo požadavku změnami, které chcete provést ve vlastní roli.

    Následující text ukazuje příklad těla požadavku s novou akcí nastavení diagnostiky:

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

Chcete-li odstranit vlastní roli, použijte [definice rolí – odstranit](/rest/api/authorization/roledefinitions/delete) rozhraní REST API. Chcete-li volat toto rozhraní API, musíte být přihlášeni `Microsoft.Authorization/roleDefinitions/delete` k uživateli, kterému je přiřazena role, která má oprávnění ke všem rozhraním `assignableScopes`. Z předdefinovaných rolí toto oprávnění zahrnuje pouze [vlastník](built-in-roles.md#owner) a [správce přístupu uživatelů.](built-in-roles.md#user-access-administrator)

1. Pomocí [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) nebo [definice rolí – získejte](/rest/api/authorization/roledefinitions/get) rozhraní REST API k získání identifikátoru IDENTIFIKÁTOR GUID vlastní role. Další informace naleznete v části dřívější [seznam vlastních rolí.](#list-custom-roles)

1. Začněte s následujícím požadavkem:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{scope}* oborem, který chcete odstranit vlastní roli.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Skupina prostředků |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |

1. Nahraďte *{roleDefinitionId}* identifikátorem identifikátoru GUID vlastní role.

## <a name="next-steps"></a>Další kroky

- [Vlastní role pro prostředky Azure](custom-roles.md)
- [Správa přístupu k prostředkům Azure pomocí RBAC a rozhraní REST API](role-assignments-rest.md)
- [Odkaz na rozhraní API Azure REST](/rest/api/azure/)
