---
title: Principy řízení přístupu na základě rolí – Azure Digital Twins | Dokumenty společnosti Microsoft
description: Přečtěte si o řízení přístupu na základě rolí a oprávnění ke správě v Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: feda4b3a7f21b581fb4f08aec013f87c0fabb7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044918"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Řízení přístupu na základě rolí v Azure Digital Twins

Azure Digital Twins umožňuje přesnou kontrolu přístupu nad konkrétními daty, prostředky a akcemi v prostorovém grafu. Činí tak prostřednictvím podrobné role a správa oprávnění s názvem [řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC). RBAC se skládá z _rolí_ a _přiřazení rolí_. Role identifikují úroveň oprávnění. Přiřazení rolí přidruží roli k uživateli nebo zařízení.

Pomocí RBAC, oprávnění může být uděleno:

- Uživatel.
- Zařízení.
- Instanční objekt.
- Uživatelem definovaná funkce.
- Všichni uživatelé, kteří patří do domény.
- Nájemník.

Stupeň přístupu lze také doladit.

RBAC je jedinečný v tom, že oprávnění jsou zděděna v prostorovém grafu.

## <a name="what-can-i-do-with-rbac"></a>Co mi RBAC umožňuje?

Vývojář může použít RBAC k:

- Udělte uživateli možnost spravovat zařízení pro celou budovu nebo pouze pro určitou místnost nebo podlahu.
- Udělte správci globální přístup ke všem uzlům prostorového grafu pro celý graf nebo pouze pro část grafu.
- Udělte specialistovi podpory přístup ke čtení v grafu, s výjimkou přístupových kláves.
- Udělit každému členovi domény přístup pro čtení ke všem objektům grafu.

## <a name="rbac-best-practices"></a>Osvědčené postupy RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Role

### <a name="role-definitions"></a>Definice rolí

Definice role je kolekce oprávnění a další atributy, které tvoří roli. Definice role uvádí povolené operace, které zahrnují *CREATE*, *READ*, *UPDATE*a *DELETE,* které mohou provádět libovolný objekt s tou rolí. Také určuje, na které typy objektů se oprávnění vztahují.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Chcete-li načíst úplné definice pro předchozí role, dotaz systému nebo role rozhraní API.
> Další informace najdete v části [Vytváření a správa přiřazení rolí](./security-create-manage-role-assignments.md#retrieve-all-roles).

### <a name="object-identifier-types"></a>Typy identifikátorů objektů

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Přečtěte si knihu Vytvoření a [správa přiřazení rolí najdete](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal)v článku Jak udělit oprávnění instančnímu objektu .

Následující referenční dokumentační články popisují:

- Jak [dotaz nebo ID objektu pro uživatele](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Jak [získat ID objektu pro instanční objekt](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Jak [načíst ID objektu pro klienta Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Přiřazení rolí

Přiřazení role Azure Digital Twins přidruží objekt, například uživatele nebo klienta Azure AD, k roli a prostoru. Oprávnění jsou udělena všem objektům, které patří do tohoto prostoru. Prostor zahrnuje celý prostorový graf pod ním.

Uživateli je například přiděleno přiřazení `DeviceInstaller` role s rolí pro kořenový uzel prostorového grafu, který představuje budovu. Uživatel pak může číst a aktualizovat zařízení pro tento uzel a všechny ostatní podřízené prostory v budově.

Chcete-li příjemci udělit oprávnění, vytvořte přiřazení role. Chcete-li odvolat oprávnění, odeberte přiřazení role.

>[!IMPORTANT]
> Další informace o přiřazení rolí naleznete v části [Vytváření a správa přiřazení rolí](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Další kroky

- Další informace o vytváření a správě přiřazení rolí Azure Digital Twins najdete v části [Vytvoření a správa přiřazení rolí](./security-create-manage-role-assignments.md).

- Přečtěte si další informace o [RBAC pro Azure](https://docs.microsoft.com/azure/role-based-access-control/).
