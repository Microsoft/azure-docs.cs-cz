---
title: Vypsat přiřazení odmítnutí Azure pomocí REST API – Azure RBAC
description: Přečtěte si, jak vypsat přiřazení odmítnutí Azure pro uživatele, skupiny a aplikace pomocí REST API a řízení přístupu na základě role Azure (Azure RBAC).
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
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2f835c270930734bf9963a7c7c3168b873eddaf6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84791907"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>Výpis přiřazení odmítnutí Azure pomocí REST API

[Přiřazení Azure Deny](deny-assignments.md) zablokují uživatelům, aby prováděli konkrétní akce prostředku Azure i v případě, že jim přiřazení role udělí přístup. Tento článek popisuje, jak vypsat přiřazení zamítnutí pomocí REST API.

> [!NOTE]
> Nemůžete přímo vytvořit vlastní přiřazení zamítnutí. Informace o tom, jak se vytvářejí přiřazení odepřít, najdete v tématu [přiřazení odmítnutí Azure](deny-assignments.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li získat informace o přiřazení zamítnutí, je nutné mít následující:

- `Microsoft.Authorization/denyAssignments/read` oprávnění, které je součástí většiny [předdefinovaných rolí Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Vypsat jedno přiřazení zamítnutí

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam přiřazení odepřít.

    > [!div class="mx-tableFixed"]
    > | Obor | Typ |
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
    > | Obor | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Předplatné |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{Filter}* podmínkou, kterou chcete použít k filtrování seznamu přiřazení odepřít.

    > [!div class="mx-tableFixed"]
    > | Filtrovat | Description |
    > | --- | --- |
    > | (žádný filtr) | Zobrazí seznam všech přiřazení odepřít v, výše a pod zadaným oborem. |
    > | `$filter=atScope()` | Vypíše odmítnutá přiřazení jenom pro zadaný rozsah a vyšší. Nezahrnuje přiřazení zamítnutí v podoborech. |
    > | `$filter=assignedTo('{objectId}')` | Zobrazí seznam zamítnutých přiřazení pro zadaného uživatele nebo instanční objekt.<br/>Pokud je uživatel členem skupiny, která má přiřazení odepřít, je také uvedeno toto přiřazení odepření. Tento filtr je přenosný pro skupiny, což znamená, že pokud je uživatel členem skupiny a tato skupina je členem jiné skupiny, která má přiřazení zamítnutí, toto přiřazení odmítnutí je také uvedeno.<br/>Tento filtr přijímá pouze ID objektu uživatele nebo instančního objektu. Nelze předat ID objektu pro skupinu. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Zobrazí seznam zamítnutých přiřazení pro zadaného uživatele nebo instanční objekt a v zadaném oboru. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Zobrazí seznam zamítnutých přiřazení se zadaným názvem. |
    > | `$filter=principalId+eq+'{objectId}'` | Zobrazí seznam zamítnutých přiřazení pro zadaného uživatele, skupinu nebo instanční objekt. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Vypsat přiřazení zamítnutí v kořenovém oboru (/)

1. Zvyšte přístup tak, jak je popsáno v tématu [zvýšení přístupu ke správě všech předplatných Azure a skupin pro správu](elevate-access-global-admin.md).

1. Použijte následující žádost:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Nahraďte *{Filter}* podmínkou, kterou chcete použít k filtrování seznamu přiřazení odepřít. Je vyžadován filtr.

    > [!div class="mx-tableFixed"]
    > | Filtrovat | Description |
    > | --- | --- |
    > | `$filter=atScope()` | Vypíše přiřazení zamítnutá jenom pro kořenový obor. Nezahrnuje přiřazení zamítnutí v podoborech. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Vypíše zamítnutá přiřazení se zadaným názvem. |

1. Odeberte přístup se zvýšenými oprávněními.

## <a name="next-steps"></a>Další kroky

- [Pochopení přiřazení Azure Deny](deny-assignments.md)
- [Zvýšení úrovně přístupu pro správu všech předplatných Azure a skupin pro správu](elevate-access-global-admin.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)
