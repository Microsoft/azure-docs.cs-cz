---
title: Seznam přiřazení rolí pomocí Azure RBAC a REST API
description: Naučte se, jak určit, které prostředky uživatelé, skupiny, instanční objekty nebo spravované identity mají přístup k používání řízení přístupu na základě role (RBAC) Azure a REST API.
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
ms.openlocfilehash: e20edcb5e2406c216711a2e0f696ef06e19fe21e
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710396"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Seznam přiřazení rolí pomocí Azure RBAC a REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] tomto článku se dozvíte, jak zobrazit seznam přiřazení rolí pomocí REST API.

## <a name="list-role-assignments"></a>Seznam přiřazení rolí

V části RBAC pro vypsání přístupu k seznamu se zobrazí seznam přiřazení rolí. K vypsání přiřazení rolí použijte jedno ze [seznamu přiřazení rolí – seznam](/rest/api/authorization/roleassignments/list) rozhraní REST API. K upřesnění výsledků zadáte obor a volitelný filtr.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam přiřazení rolí.

    | Rozsah | Typ |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    | `subscriptions/{subscriptionId1}` | Předplatné |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

    V předchozím příkladu je Microsoft. Web poskytovatelem prostředků, který odkazuje na instanci App Service. Podobně můžete použít jiné poskytovatele prostředků a zadat obor. Další informace najdete v tématech [poskytovatelé a typy prostředků Azure](../azure-resource-manager/resource-manager-supported-services.md) a podporované [Azure Resource Manager operace poskytovatele prostředků](resource-provider-operations.md).  
     
1. Nahraďte *{Filter}* podmínkou, kterou chcete použít k filtrování seznamu přiřazení role.

    | Filtrovat | Popis |
    | --- | --- |
    | `$filter=atScope()` | Vypíše přiřazení rolí jenom pro zadaný obor, včetně přiřazení rolí v podoborech. |
    | `$filter=principalId%20eq%20'{objectId}'` | Vypisuje přiřazení rolí pro zadaného uživatele, skupinu nebo instanční objekt. |
    | `$filter=assignedTo('{objectId}')` | Vypisuje přiřazení rolí pro zadaného uživatele nebo instanční objekt. Pokud je uživatel členem skupiny, která má přiřazení role, zobrazí se také toto přiřazení role. Tento filtr je přenosný pro skupiny, což znamená, že pokud je uživatel členem skupiny a tato skupina je členem jiné skupiny, která má přiřazení role, bude toto přiřazení role také uvedené. Tento filtr přijímá pouze ID objektu uživatele nebo instančního objektu. Nelze předat ID objektu pro skupinu. |

## <a name="next-steps"></a>Další kroky

- [Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a REST API](role-assignments-rest.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)
