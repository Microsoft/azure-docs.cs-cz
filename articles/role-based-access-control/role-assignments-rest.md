---
title: Přidání nebo odebrání přiřazení rolí s RBAC a REST API
description: Naučte se, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí řízení přístupu na základě role (RBAC) v Azure a REST API.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063000"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Tento článek popisuje, jak přiřadit role pomocí REST API.

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat nebo odebrat přiřazení rolí, je nutné mít následující:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

Pokud chcete udělit přístup, přidejte přiřazení role ve RBAC. K přidání přiřazení role použijte [přiřazení rolí – vytvořit](/rest/api/authorization/roleassignments/create) REST API a zadejte objekt zabezpečení, definici role a obor. K volání tohoto rozhraní API musíte mít přístup k této `Microsoft.Authorization/roleAssignments/write` operaci. Z předdefinovaných rolí má přístup k této operaci udělen pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Použijte REST API [Definice rolí – seznam](/rest/api/authorization/roledefinitions/list) , nebo se podívejte na [předdefinované role](built-in-roles.md) , abyste získali identifikátor definice role, kterou chcete přiřadit.

1. Pomocí nástroje GUID vygenerujte jedinečný identifikátor, který se použije pro identifikátor přiřazení role. Identifikátor má formát:`00000000-0000-0000-0000-000000000000`

1. Začněte s následujícím požadavkem a textem:

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

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem pro přiřazení role.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Prostředek |

    V předchozím příkladu je Microsoft. Web poskytovatelem prostředků, který odkazuje na instanci App Service. Podobně můžete použít jiné poskytovatele prostředků a zadat obor. Další informace najdete v tématech [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md) a podporované [Azure Resource Manager operace poskytovatele prostředků](resource-provider-operations.md).  

1. Nahraďte *{roleAssignmentName}* identifikátorem GUID přiřazení role.

1. V textu žádosti nahraďte *{Scope}* oborem pro přiřazení role.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* identifikátorem definice role.

1. Nahraďte *{principalId}* identifikátorem objektu uživatele, skupiny nebo instančního objektu, ke kterému se má přiřadit role.

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

V RBAC se přístup odebírá odebrání přiřazení role. K odebrání přiřazení role použijte [přiřazení rolí – odstranit](/rest/api/authorization/roleassignments/delete) REST API. K volání tohoto rozhraní API musíte mít přístup k této `Microsoft.Authorization/roleAssignments/delete` operaci. Z předdefinovaných rolí má přístup k této operaci udělen pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Získejte identifikátor přiřazení role (GUID). Tento identifikátor se vrátí při prvním vytvoření přiřazení role nebo ho můžete získat pomocí seznamu přiřazení rolí.

1. Začněte s následujícím požadavkem:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem pro odebrání přiřazení role.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleAssignmentName}* identifikátorem GUID přiřazení role.

## <a name="next-steps"></a>Další kroky

- [Seznam přiřazení rolí pomocí Azure RBAC a REST API](role-assignments-list-rest.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Reference k Azure REST API](/rest/api/azure/)
- [Vytváření vlastních rolí pro prostředky Azure pomocí REST API](custom-roles-rest.md)
