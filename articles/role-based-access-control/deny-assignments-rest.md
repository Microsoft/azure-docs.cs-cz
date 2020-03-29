---
title: Seznam odepřít přiřazení pro prostředky Azure s rozhraním REST API
description: Zjistěte, jak vypsat přiřazení odepření pro uživatele, skupiny a aplikace pomocí řízení přístupu na základě rolí (RBAC) pro prostředky Azure a rozhraní REST API.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0f648405a3d71bf27c64dacbb3fd78f3e9801137
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063016"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Seznam odepřít přiřazení pro prostředky Azure pomocí rozhraní REST API

[Odepřít přiřazení](deny-assignments.md) blokovat uživatelům provádět konkrétní akce prostředků Azure i v případě, že přiřazení role jim uděluje přístup. Tento článek popisuje, jak seznam odepřít přiřazení pomocí rozhraní REST API.

> [!NOTE]
> Nelze přímo vytvořit vlastní přiřazení odepřít. Informace o způsobu vytváření přiřazení odepření naleznete v tématu [Odepřít přiřazení](deny-assignments.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li získat informace o odepření přiřazení, musíte mít:

- `Microsoft.Authorization/denyAssignments/read`oprávnění, které je součástí většiny [předdefinovaných rolí pro prostředky Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Vypsat jedno odepření přiřazení

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. V rámci identifikátoru URI nahraďte *{scope}* oborem, pro který chcete uvést odepřít přiřazení.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Předplatné |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{deny-assignment-id}* identifikátorem odepřít přiřazení, který chcete načíst.

## <a name="list-multiple-deny-assignments"></a>Vypsat více přiřazení zamítnutí

1. Začněte jedním z následujících požadavků:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    S volitelnými parametry:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. V rámci identifikátoru URI nahraďte *{scope}* oborem, pro který chcete uvést odepřít přiřazení.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Předplatné |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{filter}* podmínkou, kterou chcete použít k filtrování seznamu odepřít přiřazení.

    > [!div class="mx-tableFixed"]
    > | Filtr | Popis |
    > | --- | --- |
    > | (bez filtru) | Zobrazí seznam všech odepření přiřazení na, nad a pod zadaný obor. |
    > | `$filter=atScope()` | Seznamy odepřít přiřazení pouze pro zadaný obor a výše. Nezahrnuje odepřít přiřazení v podoborech. |
    > | `$filter=assignedTo('{objectId}')` | Seznamy odepřít přiřazení pro zadaný uživatel nebo instanční objekt.<br/>Pokud je uživatel členem skupiny, která má odepřít přiřazení, toto odepření přiřazení je také uveden. Tento filtr je přenositý pro skupiny, což znamená, že pokud je uživatel členem skupiny a tato skupina je členem jiné skupiny, která má odepřít přiřazení, je toto odepření přiřazení také uvedeno.<br/>Tento filtr přijímá pouze ID objektu pro uživatele nebo instanční objekt. ID objektu nelze předat pro skupinu. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Seznamy odepřít přiřazení pro zadaný uživatel nebo instanční objekt a na zadaný obor. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Seznamy odepřít přiřazení se zadaným názvem. |
    > | `$filter=principalId+eq+'{objectId}'` | Seznamy odepřít přiřazení pro zadaného uživatele, skupiny nebo instančního objektu. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Vypsat přiřazení v kořenovém oboru (/)

1. Zvyšte přístup, jak je popsáno v [seznamu Zvýšit přístup pro globálního správce ve službě Azure Active Directory](elevate-access-global-admin.md).

1. Použijte následující žádost:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Nahraďte *{filter}* podmínkou, kterou chcete použít k filtrování seznamu odepřít přiřazení. Je vyžadován filtr.

    > [!div class="mx-tableFixed"]
    > | Filtr | Popis |
    > | --- | --- |
    > | `$filter=atScope()` | Seznam odepřít přiřazení pouze kořenový obor. Nezahrnuje odepřít přiřazení v podoborech. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Seznam odepřít přiřazení se zadaným názvem. |

1. Odeberte zvýšený přístup.

## <a name="next-steps"></a>Další kroky

- [Principy odepření přiřazení pro prostředky Azure](deny-assignments.md)
- [Zvýšení úrovně přístupu pro globálního správce v Azure Active Directory](elevate-access-global-admin.md)
- [Odkaz na rozhraní API Azure REST](/rest/api/azure/)
