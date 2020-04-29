---
title: Vypíše přiřazení zamítnutí pro prostředky Azure s REST API.
description: Přečtěte si, jak vypsat přiřazení zamítnutí pro uživatele, skupiny a aplikace pomocí řízení přístupu na základě role (RBAC) pro prostředky Azure a REST API.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063016"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Vypsat přiřazení zamítnutí pro prostředky Azure pomocí REST API

[Odmítnutí přiřazení](deny-assignments.md) zablokuje uživatelům, aby prováděli konkrétní akce prostředku Azure i v případě, že jim přiřazením role udělí přístup. Tento článek popisuje, jak vypsat přiřazení zamítnutí pomocí REST API.

> [!NOTE]
> Nemůžete přímo vytvořit vlastní přiřazení zamítnutí. Informace o tom, jak se vytvářejí přiřazení odepřít, najdete v tématu [zamítnutí](deny-assignments.md)přiřazení.

## <a name="prerequisites"></a>Požadavky

Chcete-li získat informace o přiřazení zamítnutí, je nutné mít následující:

- `Microsoft.Authorization/denyAssignments/read`oprávnění, které je součástí většiny [předdefinovaných rolí pro prostředky Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Vypsat jedno přiřazení zamítnutí

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam přiřazení odepřít.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Předplatné |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{Deny-Assignment-ID}* identifikátorem přiřazení odepření, který chcete načíst.

## <a name="list-multiple-deny-assignments"></a>Seznam více přiřazení odepřít

1. Začněte s jedním z následujících požadavků:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    S nepovinnými parametry:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam přiřazení odepřít.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Předplatné |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{Filter}* podmínkou, kterou chcete použít k filtrování seznamu přiřazení odepřít.

    > [!div class="mx-tableFixed"]
    > | Filtr | Popis |
    > | --- | --- |
    > | (žádný filtr) | Zobrazí seznam všech přiřazení odepřít v, výše a pod zadaným oborem. |
    > | `$filter=atScope()` | Vypíše odmítnutá přiřazení jenom pro zadaný rozsah a vyšší. Nezahrnuje přiřazení zamítnutí v podoborech. |
    > | `$filter=assignedTo('{objectId}')` | Zobrazí seznam zamítnutých přiřazení pro zadaného uživatele nebo instanční objekt.<br/>Pokud je uživatel členem skupiny, která má přiřazení odepřít, je také uvedeno toto přiřazení odepření. Tento filtr je přenosný pro skupiny, což znamená, že pokud je uživatel členem skupiny a tato skupina je členem jiné skupiny, která má přiřazení zamítnutí, toto přiřazení odmítnutí je také uvedeno.<br/>Tento filtr přijímá pouze ID objektu uživatele nebo instančního objektu. Nelze předat ID objektu pro skupinu. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Zobrazí seznam zamítnutých přiřazení pro zadaného uživatele nebo instanční objekt a v zadaném oboru. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Zobrazí seznam zamítnutých přiřazení se zadaným názvem. |
    > | `$filter=principalId+eq+'{objectId}'` | Zobrazí seznam zamítnutých přiřazení pro zadaného uživatele, skupinu nebo instanční objekt. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Vypsat přiřazení zamítnutí v kořenovém oboru (/)

1. Zvyšte přístup k vašemu přístupu, jak je popsáno v tématu [zvýšení přístupu pro globálního správce v Azure Active Directory](elevate-access-global-admin.md).

1. Použijte následující žádost:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Nahraďte *{Filter}* podmínkou, kterou chcete použít k filtrování seznamu přiřazení odepřít. Je vyžadován filtr.

    > [!div class="mx-tableFixed"]
    > | Filtr | Popis |
    > | --- | --- |
    > | `$filter=atScope()` | Vypíše přiřazení zamítnutá jenom pro kořenový obor. Nezahrnuje přiřazení zamítnutí v podoborech. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Vypíše zamítnutá přiřazení se zadaným názvem. |

1. Odeberte přístup se zvýšenými oprávněními.

## <a name="next-steps"></a>Další kroky

- [Pochopení přiřazení zamítnutí pro prostředky Azure](deny-assignments.md)
- [Zvýšení úrovně přístupu pro globálního správce v Azure Active Directory](elevate-access-global-admin.md)
- [Reference k Azure REST API](/rest/api/azure/)
