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
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9e6214b3cb2cdca2d80ebae43771b206e3396d8b
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137322"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Vypsat přiřazení zamítnutí pro prostředky Azure pomocí REST API

[Odmítnutí přiřazení](deny-assignments.md) zablokuje uživatelům, aby prováděli konkrétní akce prostředku Azure i v případě, že jim přiřazením role udělí přístup. Tento článek popisuje, jak vypsat přiřazení zamítnutí pomocí REST API.

> [!NOTE]
> Nemůžete přímo vytvořit vlastní přiřazení zamítnutí. Informace o tom, jak se vytvářejí přiřazení odepřít, najdete v tématu [zamítnutí](deny-assignments.md)přiřazení.

## <a name="prerequisites"></a>Předpoklady

Chcete-li získat informace o přiřazení zamítnutí, je nutné mít následující:

- `Microsoft.Authorization/denyAssignments/read` oprávnění, které je součástí většiny [předdefinovaných rolí pro prostředky Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Vypsat jedno přiřazení zamítnutí

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete zobrazit seznam přiřazení odepřít.

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

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

    | Rozsah | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Předplatné |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Prostředek |

1. Nahraďte *{Filter}* podmínkou, kterou chcete použít k filtrování seznamu přiřazení odepřít.

    | Filtr | Popis |
    | --- | --- |
    | (žádný filtr) | Vypíše všechna přiřazení odepřít v, výše a pod zadaným oborem. |
    | `$filter=atScope()` | Vypíše přiřazení zamítnutá jenom pro zadaný obor a výše. Nezahrnuje přiřazení zamítnutí v podoborech. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Vypíše zamítnutá přiřazení se zadaným názvem. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Vypsat přiřazení zamítnutí v kořenovém oboru (/)

1. Zvyšte přístup k vašemu přístupu, jak je popsáno v tématu [zvýšení přístupu pro globálního správce v Azure Active Directory](elevate-access-global-admin.md).

1. Použijte následující požadavek:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Nahraďte *{Filter}* podmínkou, kterou chcete použít k filtrování seznamu přiřazení odepřít. Je vyžadován filtr.

    | Filtr | Popis |
    | --- | --- |
    | `$filter=atScope()` | Vypíše přiřazení zamítnutá jenom pro kořenový obor. Nezahrnuje přiřazení zamítnutí v podoborech. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Vypíše zamítnutá přiřazení se zadaným názvem. |

1. Odeberte přístup se zvýšenými oprávněními.

## <a name="next-steps"></a>Další kroky

- [Pochopení přiřazení zamítnutí pro prostředky Azure](deny-assignments.md)
- [Zvýšení úrovně přístupu pro globálního správce v Azure Active Directory](elevate-access-global-admin.md)
- [Reference k rozhraní Azure REST API](/rest/api/azure/)
