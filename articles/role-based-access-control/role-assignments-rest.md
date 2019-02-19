---
title: Správa přístupu k Azure resoruces pomocí RBAC a rozhraní REST API – Azure | Dokumentace Microsoftu
description: Další informace o správě přístupu k prostředkům Azure pro uživatele, skupiny a aplikace pomocí řízení přístupu na základě role (RBAC) a rozhraní REST API. To zahrnuje výpis přístupu, udělení přístupu a odebrání přístupu.
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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c2ef9b0070cc9ac190b773f023ffc18d1b251a41
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338406"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Správa přístupu k prostředkům Azure pomocí RBAC a rozhraní REST API

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, která můžete spravovat přístup k prostředkům Azure. Tento článek popisuje, jak spravovat přístup pro uživatele, skupiny a aplikace pomocí RBAC a rozhraní REST API.

## <a name="list-access"></a>Výpis přístupu

V RBAC pro přístup k seznamu, můžete seznam přiřazení rolí. Chcete-li seznam přiřazení rolí, použijte jednu z [přiřazení rolí – seznam](/rest/api/authorization/roleassignments/list) rozhraní REST API. K upřesnění výsledků, zadejte obor a volitelný filtr. Pro volání rozhraní API, musíte mít přístup k `Microsoft.Authorization/roleAssignments/read` operace v zadaném oboru. Několik [předdefinované role pro prostředky Azure](built-in-roles.md) je udělen přístup k této operace.

1. Začněte s následující žádosti:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s rozsahem, pro které chcete seznam přiřazení rolí.

    | Rozsah | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{filter}* splňující podmínku, kterou chcete použít k filtrování seznamu přiřazení role.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=atScope()` | Seznam přiřazení rolí pro pouze zadaný obor, bez zahrnutí přiřazení rolí na subscopes. |
    | `$filter=principalId%20eq%20'{objectId}'` | Seznam přiřazení rolí pro konkrétního uživatele, skupinu nebo instanční objekt služby. |
    | `$filter=assignedTo('{objectId}')` | Seznam přiřazení rolí pro zadaného uživatele, včetně těch zděděných ze skupin. |

## <a name="grant-access"></a>Udělení přístupu

V RBAC se přístup uděluje vytvořením přiřazení role. Chcete-li vytvořit přiřazení role, použijte [vytvořit přiřazení Role –](/rest/api/authorization/roleassignments/create) rozhraní REST API a zadat objekt zabezpečení, definice role a obor. K volání tohoto rozhraní API, musíte mít přístup k `Microsoft.Authorization/roleAssignments/write` operace. Předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) je udělen přístup k této operace.

1. Použití [definice rolí – seznam](/rest/api/authorization/roledefinitions/list) rozhraní REST API nebo vyhledejte další [předdefinované role](built-in-roles.md) získat identifikátor pro definici role, kterou chcete přiřadit.

1. Generovat jedinečný identifikátor, který se použije pro identifikátor přiřazení role pomocí nástroje identifikátor GUID. Tento identifikátor má následující formát: `00000000-0000-0000-0000-000000000000`

1. Začněte s následující žádosti a text:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. V rámci identifikátoru URI nahradit *{oboru}* s oborem pro přiřazení role.

    | Rozsah | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleAssignmentName}* s identifikátorem GUID přiřazení role.

1. V těle požadavku nahradit *{subscriptionId}* identifikátorem předplatného.

1. Nahraďte *{roleDefinitionId}* s identifikátorem definice role.

1. Nahraďte *{principalId}* s identifikátorem objektu uživatele, skupiny nebo instanční objekt, který bude mít přiřazenou roli.

## <a name="remove-access"></a>Odebrání přístupu

V RBAC se přístup odebírá odebrání přiřazení role. Pokud chcete odebrat přiřazení role, použít [odstranit přiřazení rolí –](/rest/api/authorization/roleassignments/delete) rozhraní REST API. K volání tohoto rozhraní API, musíte mít přístup k `Microsoft.Authorization/roleAssignments/delete` operace. Předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) je udělen přístup k této operace.

1. Získáte přiřazení role identifikátor (GUID). Tento identifikátor je vrácena, pokud nejprve vytvořit přiřazení role nebo ho můžete získat seznam přiřazení rolí.

1. Začněte s následující žádosti:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s oborem pro odebrání přiřazení role.

    | Rozsah | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleAssignmentName}* s identifikátorem GUID přiřazení role.

## <a name="next-steps"></a>Další postup

- [Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)
- [Vytvoření vlastních rolí pro prostředky Azure pomocí rozhraní REST API](custom-roles-rest.md)