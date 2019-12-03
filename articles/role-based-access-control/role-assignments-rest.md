---
title: Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a REST API
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1ba0c2bd81f32c0aec242dbfb32b2d7f4064ddbe
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707831"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] tomto článku se dozvíte, jak přiřadit role pomocí REST API.

## <a name="prerequisites"></a>Předpoklady

Chcete-li přidat nebo odebrat přiřazení rolí, je nutné mít následující:

- oprávnění `Microsoft.Authorization/roleAssignments/write` a `Microsoft.Authorization/roleAssignments/delete`, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)

## <a name="add-a-role-assignment"></a>Přidání přiřazení role

Pokud chcete udělit přístup, přidejte přiřazení role ve RBAC. K přidání přiřazení role použijte [přiřazení rolí – vytvořit](/rest/api/authorization/roleassignments/create) REST API a zadejte objekt zabezpečení, definici role a obor. Chcete-li volat toto rozhraní API, je nutné mít přístup k operaci `Microsoft.Authorization/roleAssignments/write`. Z předdefinovaných rolí má přístup k této operaci udělen pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Použijte REST API [Definice rolí – seznam](/rest/api/authorization/roledefinitions/list) , nebo se podívejte na [předdefinované role](built-in-roles.md) , abyste získali identifikátor definice role, kterou chcete přiřadit.

1. Pomocí nástroje GUID vygenerujte jedinečný identifikátor, který se použije pro identifikátor přiřazení role. Identifikátor má formát: `00000000-0000-0000-0000-000000000000`

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

    | Rozsah | Typ |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    | `subscriptions/{subscriptionId1}` | Předplatné |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleAssignmentName}* identifikátorem GUID přiřazení role.

1. V textu žádosti nahraďte *{Scope}* oborem pro přiřazení role.

    | Rozsah | Typ |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    | `subscriptions/{subscriptionId1}` | Předplatné |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleDefinitionId}* identifikátorem definice role.

1. Nahraďte *{principalId}* identifikátorem objektu uživatele, skupiny nebo instančního objektu, ke kterému se má přiřadit role.

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

V RBAC se přístup odebírá odebrání přiřazení role. K odebrání přiřazení role použijte [přiřazení rolí – odstranit](/rest/api/authorization/roleassignments/delete) REST API. Chcete-li volat toto rozhraní API, je nutné mít přístup k operaci `Microsoft.Authorization/roleAssignments/delete`. Z předdefinovaných rolí má přístup k této operaci udělen pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Získejte identifikátor přiřazení role (GUID). Tento identifikátor se vrátí při prvním vytvoření přiřazení role nebo ho můžete získat pomocí seznamu přiřazení rolí.

1. Začněte s následujícím požadavkem:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem pro odebrání přiřazení role.

    | Rozsah | Typ |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    | `subscriptions/{subscriptionId1}` | Předplatné |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleAssignmentName}* identifikátorem GUID přiřazení role.

## <a name="next-steps"></a>Další kroky

- [Seznam přiřazení rolí pomocí Azure RBAC a REST API](role-assignments-list-rest.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)
- [Vytváření vlastních rolí pro prostředky Azure pomocí REST API](custom-roles-rest.md)
