---
title: Přidání nebo odebrání přiřazení rolí pomocí rbacu a rozhraní REST API
description: Zjistěte, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí řízení přístupu na základě rolí Azure (RBAC) a rozhraní REST API.
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
ms.openlocfilehash: 9beda6589c03f1b14fc9756af86a9ce0711894c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063000"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a rozhraní REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Tento článek popisuje, jak přiřadit role pomocí rozhraní REST API.

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat nebo odebrat přiřazení rolí, musíte mít:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, například [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) nebo [Vlastník](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

V RBAC, chcete-li udělit přístup, přidáte přiřazení role. Chcete-li přidat přiřazení role, použijte [přiřazení rolí – vytvořte](/rest/api/authorization/roleassignments/create) rozhraní REST API a zadejte objekt zabezpečení, definici role a obor. Chcete-li volat toto rozhraní `Microsoft.Authorization/roleAssignments/write` API, musíte mít přístup k operaci. Z předdefinovaných rolí je k této operaci udělen přístup pouze [vlastník](built-in-roles.md#owner) a [správce přístupu uživatelů.](built-in-roles.md#user-access-administrator)

1. Pomocí [rozhraní ROLE – seznam](/rest/api/authorization/roledefinitions/list) rozhraní REST API nebo v části [Předdefinované role](built-in-roles.md) získáte identifikátor pro definici role, kterou chcete přiřadit.

1. Pomocí nástroje GUID vygenerujte jedinečný identifikátor, který bude použit pro identifikátor přiřazení role. Identifikátor má formát:`00000000-0000-0000-0000-000000000000`

1. Začněte s následujícím požadavkem a tělem:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. V rámci identifikátoru URI nahraďte *{scope}* rozsahem pro přiřazení role.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Prostředek |

    V předchozím příkladu microsoft.web je poskytovatel prostředků, který odkazuje na instanci služby App Service. Podobně můžete použít jiné zprostředkovatele prostředků a určit obor. Další informace najdete [v tématu Zprostředkovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md) a podporované operace [zprostředkovatele prostředků Azure Resource Manager](resource-provider-operations.md).  

1. Nahraďte *{roleAssignmentName}* identifikátorem GUID přiřazení role.

1. V těle požadavku nahraďte *{scope}* rozsahem pro přiřazení role.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* identifikátorem definice role.

1. Nahraďte *{principalId}* identifikátorem objektu uživatele, skupiny nebo instančního objektu, kterému bude přiřazena role.

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

V RBAC se přístup odebírá odebrání přiřazení role. Chcete-li odebrat přiřazení role, použijte [přiřazení rolí – odstranit](/rest/api/authorization/roleassignments/delete) rozhraní REST API. Chcete-li volat toto rozhraní `Microsoft.Authorization/roleAssignments/delete` API, musíte mít přístup k operaci. Z předdefinovaných rolí je k této operaci udělen přístup pouze [vlastník](built-in-roles.md#owner) a [správce přístupu uživatelů.](built-in-roles.md#user-access-administrator)

1. Získejte identifikátor přiřazení role (GUID). Tento identifikátor je vrácen při prvním vytvoření přiřazení role nebo jej můžete získat výpisem přiřazení rolí.

1. Začněte s následujícím požadavkem:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{scope}* rozsahem pro odebrání přiřazení role.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleAssignmentName}* identifikátorem GUID přiřazení role.

## <a name="next-steps"></a>Další kroky

- [Seznam přiřazení rolí pomocí Azure RBAC a rozhraní REST API](role-assignments-list-rest.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Odkaz na rozhraní API Azure REST](/rest/api/azure/)
- [Vytvoření vlastních rolí pro prostředky Azure pomocí rozhraní REST API](custom-roles-rest.md)
