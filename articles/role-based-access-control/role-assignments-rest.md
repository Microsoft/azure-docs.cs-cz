---
title: Správa přístupu pomocí RBAC a rozhraní API REST - Azure | Microsoft Docs
description: Zjistěte, jak chcete spravovat přístup pro uživatele, skupiny a aplikace, pomocí rozhraní REST API a řízení přístupu na základě rolí (RBAC). To zahrnuje jak přístup, udělení přístupu a odebrání přístupu.
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
ms.topic: article
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cfcb87fdff8105b25d4f7e63b775aaf9243d2a90
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317003"
---
# <a name="manage-access-using-rbac-and-the-rest-api"></a>Správa přístupu pomocí RBAC a rozhraní REST API

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, která můžete spravovat přístup k prostředkům v Azure. Tento článek popisuje, jak spravovat přístup pro uživatele, skupiny a aplikace s použitím RBAC a rozhraní REST API.

## <a name="list-access"></a>Přístup k seznamu

V RBAC pro přístup k seznamu, můžete seznam přiřazení rolí. Chcete-li seznam přiřazení rolí, použijte jednu z [přiřazení rolí - seznamu](/rest/api/authorization/roleassignments/list) rozhraní REST API. Upřesňující výsledky, zadejte obor a volitelný filtr. Pro volání rozhraní API, musíte mít přístup k `Microsoft.Authorization/roleAssignments/read` operace v zadaném oboru. Několik [předdefinované role](built-in-roles.md) mají udělen přístup k této operaci.

1. Můžete začít s následující požadavek:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. V rámci identifikátoru URI, nahraďte *{oboru}* s oborem, pro které chcete zobrazit seznam přiřazení rolí.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{filtru}* s podmínku, kterou chcete použít pro filtrování seznamu přiřazení role.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=atScope()` | Zobrazí seznam přiřazení rolí pro pouze zadaný obor, není včetně přiřazení rolí v subscopes. |
    | `$filter=principalId%20eq%20'{objectId}'` | Zobrazí seznam přiřazení rolí pro zadaného uživatele, skupiny nebo objektu služby. |
    | `$filter=assignedTo('{objectId}')` | Zobrazí seznam přiřazení rolí pro zadaného uživatele, včetně těch, které jsou zděděno od skupiny. |

## <a name="grant-access"></a>Udělení přístupu

V RBAC udělit přístup, můžete vytvořit přiřazení role. Chcete-li vytvořit přiřazení role, použijte [vytvořit přiřazení Role -](/rest/api/authorization/roleassignments/create) REST API a zadejte objekt zabezpečení, definice role a obor. Toto rozhraní API volat, musí mít přístup k `Microsoft.Authorization/roleAssignments/write` operaci. Z předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce přístupu uživatelů](built-in-roles.md#user-access-administrator) mají udělen přístup k této operaci.

1. Použití [definice rolí - seznamu](/rest/api/authorization/roledefinitions/list) REST API nebo najdete [předdefinované role](built-in-roles.md) získat identifikátor pro definici role, kterou chcete přiřadit.

1. Generovat jedinečný identifikátor, který se použije pro identifikátor přiřazení role pomocí nástroje identifikátor GUID. Identifikátor má formát: `00000000-0000-0000-0000-000000000000`

1. Můžete začít s následující žádosti a text:

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
    
1. V rámci identifikátoru URI, nahraďte *{oboru}* s oborem pro přiřazení role.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleAssignmentName}* s identifikátorem GUID přiřazení role.

1. V těle žádosti nahradit *{subscriptionId}* s ID vašeho předplatného.

1. Nahraďte *{hodnoty vlastnosti roleDefinitionId}* s identifikátorem definici role.

1. Nahraďte *{principalId}* s identifikátor objektu uživatele, skupiny nebo instanční objekt, který se přiřadí role.

## <a name="remove-access"></a>Odebrat přístup

V RBAC k odebrání přístupu, odeberete přiřazení role. Pokud chcete odstranit přiřazení role, použijte [odstranit přiřazení Role -](/rest/api/authorization/roleassignments/delete) REST API. Toto rozhraní API volat, musí mít přístup k `Microsoft.Authorization/roleAssignments/delete` operaci. Z předdefinovaných rolí pouze [vlastníka](built-in-roles.md#owner) a [správce přístupu uživatelů](built-in-roles.md#user-access-administrator) mají udělen přístup k této operaci.

1. Získáte přiřazení role identifikátor (GUID). Tento identifikátor je vrácena, pokud nejprve vytvořit přiřazení role nebo je můžete získat tak, že uvedete přiřazení rolí.

1. Můžete začít s následující požadavek:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI, nahraďte *{oboru}* s oborem pro odebrání přiřazení role.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{roleAssignmentName}* s identifikátorem GUID přiřazení role.

## <a name="next-steps"></a>Další postup

- [Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Referenční dokumentace rozhraní API Azure REST](/rest/api/azure/)
- [Vytvoření vlastních rolí pomocí rozhraní REST API](custom-roles-rest.md)