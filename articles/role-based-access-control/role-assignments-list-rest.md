---
title: Seznam přiřazení rolí pomocí Azure RBAC a rozhraní REST API
description: Zjistěte, jak určit, k jakým prostředkům mají uživatelé, skupiny, instanční objekty nebo spravované identity přístup pomocí řízení přístupu azure založené na rolích (RBAC) a rozhraní REST API.
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
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062160"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Seznam přiřazení rolí pomocí Azure RBAC a rozhraní REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Tento článek popisuje, jak seznam přiřazení rolí pomocí rozhraní REST API.

> [!NOTE]
> Pokud vaše organizace zadala funkce správy poskytovateli služeb, který používá [azure delegovanou správu prostředků](../lighthouse/concepts/azure-delegated-resource-management.md), přiřazení rolí autorizovaná tímto poskytovatelem služeb se zde nezobrazí.

## <a name="list-role-assignments"></a>Zobrazení seznamu přiřazení rolí

V RBAC, chcete-li seznam přístupu, seznam přiřazení rolí. Chcete-li vypsat přiřazení rolí, použijte jedno z [úloh přiřazení - seznam](/rest/api/authorization/roleassignments/list) rest API. Chcete-li upřesnit výsledky, zadejte obor a volitelný filtr.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahraďte *{scope}* oborem, pro který chcete uvést přiřazení rolí.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Prostředek |

    V předchozím příkladu microsoft.web je poskytovatel prostředků, který odkazuje na instanci služby App Service. Podobně můžete použít jiné zprostředkovatele prostředků a určit obor. Další informace najdete [v tématu Zprostředkovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md) a podporované operace [zprostředkovatele prostředků Azure Resource Manager](resource-provider-operations.md).  
     
1. Nahraďte *{filter}* podmínkou, kterou chcete použít k filtrování seznamu přiřazení rolí.

    > [!div class="mx-tableFixed"]
    > | Filtr | Popis |
    > | --- | --- |
    > | `$filter=atScope()` | Uvádí přiřazení rolí pouze pro zadaný obor, bez přiřazení rolí v podoborech. |
    > | `$filter=assignedTo('{objectId}')` | Zobrazí seznam přiřazení rolí pro zadaný uživatel nebo instanční objekt.<br/>Pokud je uživatel členem skupiny, která má přiřazení role, je toto přiřazení role také uvedeno. Tento filtr je přenositý pro skupiny, což znamená, že pokud je uživatel členem skupiny a tato skupina je členem jiné skupiny, která má přiřazení role, je toto přiřazení role také uvedeno.<br/>Tento filtr přijímá pouze ID objektu pro uživatele nebo instanční objekt. ID objektu nelze předat pro skupinu. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Uvádí přiřazení rolí pro zadaný uživatel nebo instanční objekt a v zadaném oboru. |
    > | `$filter=principalId+eq+'{objectId}'` | Zobrazí seznam přiřazení rolí pro zadaného uživatele, skupinu nebo instanční objekt. |

## <a name="next-steps"></a>Další kroky

- [Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a rozhraní REST API](role-assignments-rest.md)
- [Odkaz na rozhraní API Azure REST](/rest/api/azure/)
