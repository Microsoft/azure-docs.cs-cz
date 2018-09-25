---
title: Seznam zamítnout přiřazení pomocí RBAC a rozhraní REST API – Azure | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit seznam zamítnout přiřazení pro uživatele, skupiny nebo aplikace, pomocí řízení přístupu na základě role (RBAC) a rozhraní REST API.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 44c1d3b18bb9bdc63247379fe3f277cb6542f2da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975578"
---
# <a name="list-deny-assignments-using-rbac-and-the-rest-api"></a>Seznam zamítnout přiřazení pomocí RBAC a rozhraní REST API

V současné době zamítnout přiřazení **jen pro čtení** a lze nastavit pouze v Azure. I v případě, že nemůžete vytvořit vlastní zamítnout přiřazení, můžete vytvořit seznam zamítnout přiřazení, protože se může mít vliv na vaše skutečná oprávnění. Tento článek popisuje, jak lze uvést zamítnout přiřazení pomocí RBAC a rozhraní REST API.

## <a name="list-a-single-deny-assignment"></a>Seznam jedné zamítnout přiřazení

1. Začněte s následující žádosti:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s rozsahem, pro které chcete seznam přiřazení odepřít.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{odepřít id přiřazení}* s identifikátorem Odepřít přiřazení, které chcete načíst.

## <a name="list-multiple-deny-assignments"></a>Seznam více zamítnout přiřazení

1. Začněte s jedním z následujících požadavků:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    S volitelnými parametry:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. V rámci identifikátoru URI nahradit *{oboru}* s rozsahem, pro které chcete seznam přiřazení odepřít.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{filter}* splňující podmínku, kterou chcete použít k filtrování seznamu přiřazení odepřít.

    | Filtr | Popis |
    | --- | --- |
    | (žádný filtr) | Seznam všech zamítnout přiřazení na výše a níže zadaném oboru. |
    | `$filter=atScope()` | Seznam zamítnout přiřazení pro zadaný obor a vyšší. Nezahrnuje přiřazení odepřít v subscopes. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Seznam zamítnout přiřazení se zadaným názvem. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Seznam zamítnout přiřazení v kořenovém oboru (/)

1. Zvýšení úrovně přístupu, jak je popsáno v [zvýšení úrovně přístupu pro globálního správce ve službě Azure Active Directory](elevate-access-global-admin.md).

1. Pomocí následující žádosti:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Nahraďte *{filter}* splňující podmínku, kterou chcete použít k filtrování seznamu přiřazení odepřít. Filtr je povinný.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=atScope()` | Seznam zamítnout přiřazení kořenového oboru. Nezahrnuje přiřazení odepřít v subscopes. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Seznam zamítnout přiřazení se zadaným názvem. |

1. Odeberte přístup se zvýšeným oprávněním.

## <a name="next-steps"></a>Další postup

- [Vysvětlení zamítnout přiřazení](deny-assignments.md)
- [Zvýšení úrovně přístupu pro globálního správce ve službě Azure Active Directory](elevate-access-global-admin.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)
