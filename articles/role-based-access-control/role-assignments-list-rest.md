---
title: Vypsání přiřazení rolí Azure pomocí REST API – Azure RBAC
description: Naučte se určit, k jakým prostředkům uživatelé, skupiny, instanční objekty nebo spravované identity mají přístup pomocí REST API a řízení přístupu na základě role Azure (Azure RBAC).
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
ms.openlocfilehash: 634e1111c9374a1749e7dbb0666740ce2833a688
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84790972"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>Vypsání přiřazení rolí Azure pomocí REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Tento článek popisuje, jak zobrazit seznam přiřazení rolí pomocí REST API.

> [!NOTE]
> Pokud má vaše organizace samoobslužné funkce správy pro poskytovatele služeb, který používá [správu delegovaných prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md), tady se nezobrazí přiřazení rolí autorizovaných tímto poskytovatelem služeb.

## <a name="list-role-assignments"></a>Zobrazení seznamu přiřazení rolí

V Azure RBAC pro vypsání přístupu k seznamu získáte seznam přiřazení rolí. K vypsání přiřazení rolí použijte jedno ze [seznamu přiřazení rolí – seznam](/rest/api/authorization/roleassignments/list) rozhraní REST API. K upřesnění výsledků zadáte obor a volitelný filtr.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam přiřazení rolí.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Prostředek |

    V předchozím příkladu je Microsoft. Web poskytovatelem prostředků, který odkazuje na instanci App Service. Podobně můžete použít jiné poskytovatele prostředků a zadat obor. Další informace najdete v tématech [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md) a podporované [Azure Resource Manager operace poskytovatele prostředků](resource-provider-operations.md).  
     
1. Nahraďte *{Filter}* podmínkou, kterou chcete použít k filtrování seznamu přiřazení role.

    > [!div class="mx-tableFixed"]
    > | Filtrovat | Description |
    > | --- | --- |
    > | `$filter=atScope()` | Vypíše přiřazení rolí jenom pro zadaný obor, včetně přiřazení rolí v podoborech. |
    > | `$filter=assignedTo('{objectId}')` | Vypisuje přiřazení rolí pro zadaného uživatele nebo instanční objekt.<br/>Pokud je uživatel členem skupiny, která má přiřazení role, zobrazí se také toto přiřazení role. Tento filtr je přenosný pro skupiny, což znamená, že pokud je uživatel členem skupiny a tato skupina je členem jiné skupiny, která má přiřazení role, bude toto přiřazení role také uvedené.<br/>Tento filtr přijímá pouze ID objektu uživatele nebo instančního objektu. Nelze předat ID objektu pro skupinu. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Zobrazuje přiřazení rolí pro zadaného uživatele nebo instanční objekt a v zadaném oboru. |
    > | `$filter=principalId+eq+'{objectId}'` | Vypisuje přiřazení rolí pro zadaného uživatele, skupinu nebo instanční objekt. |

Následující požadavek Vypíše všechna přiřazení rolí pro zadaného uživatele v oboru předplatného:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()+and+assignedTo('{objectId1}')
```

Následující příklad ukazuje příklad výstupu:

```json
{
    "value": [
        {
            "properties": {
                "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
                "principalId": "{objectId1}",
                "scope": "/subscriptions/{subscriptionId1}",
                "createdOn": "2019-01-15T21:08:45.4904312Z",
                "updatedOn": "2019-01-15T21:08:45.4904312Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "{roleAssignmentId1}"
        }
    ]
}
```

## <a name="next-steps"></a>Další kroky

- [Přidání nebo odebrání přiřazení rolí v Azure pomocí REST API](role-assignments-rest.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)
