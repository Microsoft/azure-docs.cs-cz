---
title: Přidání nebo odebrání přiřazení rolí Azure pomocí REST API – Azure RBAC
description: Přečtěte si, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí REST API a řízení přístupu na základě role Azure (Azure RBAC).
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
ms.date: 05/06/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e4f230663e0eeddcf874c24e5041653f241f481c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964265"
---
# <a name="add-or-remove-azure-role-assignments-using-the-rest-api"></a>Přidání nebo odebrání přiřazení rolí v Azure pomocí REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Tento článek popisuje, jak přiřadit role pomocí REST API.

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat nebo odebrat přiřazení rolí, je nutné mít následující:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

Když v Azure RBAC udělíte přístup, přidáte přiřazení role. K přidání přiřazení role použijte [přiřazení rolí – vytvořit](/rest/api/authorization/roleassignments/create) REST API a zadejte objekt zabezpečení, definici role a obor. K volání tohoto rozhraní API musíte mít přístup k této `Microsoft.Authorization/roleAssignments/write` operaci. Z předdefinovaných rolí má přístup k této operaci udělen pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Použijte REST API [Definice rolí – seznam](/rest/api/authorization/roledefinitions/list) , nebo se podívejte na [předdefinované role](built-in-roles.md) , abyste získali identifikátor definice role, kterou chcete přiřadit.

1. Pomocí nástroje GUID vygenerujte jedinečný identifikátor, který se použije pro identifikátor přiřazení role. Identifikátor má formát: `00000000-0000-0000-0000-000000000000`

1. Začněte s následujícím požadavkem a textem:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
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

    V předchozím příkladu je Microsoft. Web poskytovatelem prostředků, který odkazuje na instanci App Service. Podobně můžete použít jiné poskytovatele prostředků a zadat obor. Další informace najdete v tématech [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md) a podporované [operace poskytovatele prostředků Azure](resource-provider-operations.md).  

1. Nahraďte *{roleAssignmentId}* identifikátorem GUID přiřazení role.

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

Následující požadavek a tělo přiřadí roli [čtečky zálohování](built-in-roles.md#backup-reader) uživateli v oboru předplatného:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

Následující příklad ukazuje příklad výstupu:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

Pokud chcete v Azure RBAC odebrat přístup, odeberte přiřazení role. K odebrání přiřazení role použijte [přiřazení rolí – odstranit](/rest/api/authorization/roleassignments/delete) REST API. K volání tohoto rozhraní API musíte mít přístup k této `Microsoft.Authorization/roleAssignments/delete` operaci. Z předdefinovaných rolí má přístup k této operaci udělen pouze [vlastník](built-in-roles.md#owner) a [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

1. Získejte identifikátor přiřazení role (GUID). Tento identifikátor se vrátí při prvním vytvoření přiřazení role nebo ho můžete získat pomocí seznamu přiřazení rolí.

1. Začněte s následujícím požadavkem:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem pro odebrání přiřazení role.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleAssignmentId}* identifikátorem GUID přiřazení role.

Následující požadavek Odstraní zadané přiřazení role v oboru předplatného:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

Následující příklad ukazuje příklad výstupu:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="next-steps"></a>Další kroky

- [Vypsání přiřazení rolí Azure pomocí REST API](role-assignments-list-rest.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)
- [Pomocí REST API vytvořit nebo aktualizovat vlastní role Azure](custom-roles-rest.md)
