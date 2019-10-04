---
title: Pochopení řízení přístupu na základě rolí – digitální vlákna Azure | Microsoft Docs
description: Přečtěte si o řízení přístupu na základě role a o správě oprávnění v digitálních prostředníkech Azure.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: lyhughes
ms.openlocfilehash: a5953aab179a62d4a8e37b7f5c7659318628918e
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827036"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Řízení přístupu na základě role v digitálních Vlákenách Azure

Digitální vlákna Azure umožňuje přesné řízení přístupu ke konkrétním datům, prostředkům a akcím v prostorovém grafu. K tomu připadá prostřednictvím podrobné správy rolí a oprávnění označovaného jako řízení přístupu na základě role (RBAC). RBAC se skládá z _rolí_ a _přiřazení rolí_. Role identifikují úroveň oprávnění. Přiřazení rolí přiřadí roli k uživateli nebo zařízení.

Pomocí RBAC se dá udělit oprávnění k těmto akcím:

- Uživatel.
- Zařízení.
- Instanční objekt
- Uživatelsky definovaná funkce.
- Všichni uživatelé patřící do domény.
- Tenant.

Stupeň přístupu lze také doladit.

RBAC je jedinečný v tom, že jsou tato oprávnění zděděná v prostorovém grafu.

## <a name="what-can-i-do-with-rbac"></a>Co můžu dělat s RBAC?

Vývojář může použít RBAC k těmto akcím:

- Udělte uživateli možnost spravovat zařízení pro celou stavbu nebo pouze pro konkrétní místnost nebo podlahu.
- Udělte globálním přístup správce ke všem uzlům prostorového grafu pro celý graf nebo pouze pro část grafu.
- Udělte odborníkovi podpory přístup pro čtení do grafu s výjimkou přístupových klíčů.
- Udělte každému členovi domény přístup pro čtení ke všem objektům grafu.

## <a name="rbac-best-practices"></a>Osvědčené postupy RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Role

### <a name="role-definitions"></a>Definice rolí

Definice role je kolekce oprávnění a dalších atributů, které tvoří roli. Definice role obsahuje seznam povolených operací, které zahrnují *vytváření*, *čtení*, *aktualizaci*a *odstraňování* všech objektů s touto rolí, které mohou provádět. Určuje také, na které typy objektů se oprávnění vztahují.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Chcete-li načíst úplné definice pro předchozí role, Dotazujte rozhraní API systému nebo rolí.
> Další informace najdete v [tématu vytváření a správa přiřazení rolí](./security-create-manage-role-assignments.md#retrieve-all-roles).

### <a name="object-identifier-types"></a>Typy identifikátorů objektů

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Přečtěte si, jak udělit oprávnění k instančnímu objektu tím, že si přečtete [vytváření a správu přiřazení rolí](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal).

V následujících tématech najdete popis těchto článků dokumentace:

- [Dotazování nebo ID objektu pro uživatele](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Jak [získat ID objektu pro instanční](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)objekt.
- Jak [Načíst ID objektu pro tenanta Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Přiřazení rolí

Přiřazení role digitálních vláken Azure přidružuje objekt, jako je uživatel nebo tenant Azure AD, s rolí a mezerou. Oprávnění jsou udělena všem objektům, které patří do daného prostoru. Prostor obsahuje celý prostorový graf pod ním.

Uživateli je například přiděleno přiřazení role s rolí `DeviceInstaller` pro kořenový uzel prostorového grafu, který představuje budova. Uživatel pak může číst a aktualizovat zařízení pro tento uzel a všechny ostatní podřízené prostory v budově.

Chcete-li udělit oprávnění příjemci, vytvořte přiřazení role. Chcete-li odvolat oprávnění, odeberte přiřazení role.

>[!IMPORTANT]
> Další informace o přiřazení rolí najdete v [tématu vytváření a správa přiřazení rolí](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Další kroky

- Pokud chcete získat další informace o vytváření a správě přiřazení rolí digitálních vláken Azure, přečtěte si téma [Vytvoření a správa přiřazení rolí](./security-create-manage-role-assignments.md).