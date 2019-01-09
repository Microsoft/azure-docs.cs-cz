---
title: Principy řízení přístupu na základě rolí Azure digitální dvojče | Dokumentace Microsoftu
description: Další ověřování v digitální dvojče s řízením přístupu na základě rolí.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: lyrana
ms.openlocfilehash: 94217deace731f6650db8003d2e1e3c6c63dcb66
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121018"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Řízení přístupu podle role v Azure digitální dvojče

Azure digitální dvojče umožňuje řízení přístupu na přesné ke konkrétní data, prostředkům a akcím v prostorových grafu. Dělá to pomocí granulární role a oprávnění správy nazývá řízení přístupu na základě role (RBAC). RBAC se skládá z _role_ a _přiřazení rolí_. Role určete úroveň oprávnění. Přiřazení rolí roli přidružit uživateli nebo zařízení.

Pomocí RBAC, oprávnění lze udělit práva:

- Uživatel.
- Zařízení.
- Objekt služby.
- Uživatelem definované funkce.
- Všichni uživatelé, kteří patří do nějaké domény.
- Tenant.

Úroveň přístupu také možné podrobně nastavit.

RBAC je jedinečný v tom, že se oprávnění dědí dolů na prostorový graf.

## <a name="what-can-i-do-with-rbac"></a>Co mi RBAC umožňuje?

Vývojáři mohou využít RBAC pro:

- Udělte schopnost spravovat zařízení pro celé sestavení, nebo jenom pro konkrétní místnost nebo dolní mez.
- Udělení globální přístup správce ke všem uzlům prostorový graf pro celý graf nebo pouze pro části grafu.
- Udělte přístup k podpoře specialista pro čtení do grafu, s výjimkou přístupové klíče.
- Udělení přístupu k doméně pro čtení ke všem objektům grafu každého člena.

## <a name="rbac-best-practices"></a>Osvědčené postupy RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Role

### <a name="role-definitions"></a>Definice rolí

Definice role je soubor oprávnění a další atributy, které tvoří roli. Definice role obsahuje povolené operace, které zahrnují *vytvořit*, *čtení*, *aktualizace*, a *odstranit* žádný objekt s ním role mohou provádět. Také určuje, ke kterému objekt typy oprávnění se vztahují na.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> K načtení úplné definice pro předchozí role, dotaz rozhraní API systému/rolí.
> Další informace najdete v [vytváření a správa přiřazení rolí](./security-create-manage-role-assignments.md#all).

### <a name="object-identifier-types"></a>Typy identifikátorů objektů

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Zjistěte, jak udělit oprávnění instančního objektu služby přečtením [vytváření a správa přiřazení rolí](./security-create-manage-role-assignments.md#grant).

V následujících referenční dokumentaci článcích:

- Jak [dotazu nebo ID objektu uživatele](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Jak [získání ID objektu pro objekt zabezpečení služby](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1).
- Jak [načíst ID objektu pro tenanta služby Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Přiřazení rolí

Přiřazení role Azure digitální dvojče objektu, například uživatele nebo tenantem Azure AD přidruží roli a mezerou. Oprávnění jsou udělena pro všechny objekty, které patří do tohoto místa. Místo zahrnuje celý prostorový graf pod ním.

Například uživatel dostane přiřazení role s rolí `DeviceInstaller` pro kořenový uzel prostorový graf, který představuje budovy. Uživatel pak číst a aktualizovat zařízení pro tento uzel a všechny ostatní podřízené prostory v budově.

Udělení oprávnění pro příjemce, vytvořte přiřazení role. Odvolat oprávnění, odeberte přiřazení role.

>[!IMPORTANT]
> Další informace o přiřazení rolí načtením [vytváření a správa přiřazení rolí](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Další postup

- Další informace o vytváření a správa Azure digitální dvojče přiřazení rolí, přečtěte si téma [vytvořit a spravovat přiřazení rolí](./security-create-manage-role-assignments.md).
