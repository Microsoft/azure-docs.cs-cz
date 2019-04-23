---
title: Vysvětlení zamítnout přiřazení pro prostředky Azure | Dokumentace Microsoftu
description: Další informace o zamítnutí přiřazení v řízení přístupu na základě role (RBAC) pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 497571a65510f806d7d7994c9dc37f9a00b65a5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197132"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Vysvětlení zamítnout přiřazení pro prostředky Azure

Přiřazení role, podobně jako *zamítnout přiřazení* akce Odepřít bude k obrazci skupinu pro uživatele, skupinu nebo instanční objekt v určitém rozsahu pro účely odepření přístupu. Zamítnout přiřazení zablokovat uživatelům provádět akce konkrétních prostředků Azure i v případě přiřazení role uděluje přístup. Některé prostředek, který poskytovatelů služeb v Azure teď obsahují zamítnout přiřazení.

V některých ohledech odeprete přiřazení se liší od přiřazení rolí. Zamítnout přiřazení můžete vyloučit objekty zabezpečení a zabránit dědění za účelem podřízených oborech. Zamítnout přiřazení platí také pro [klasický správce předplatného](rbac-and-directory-admin-roles.md) přiřazení.

Tento článek popisuje, jak zakázat přiřazení jsou definovány.

> [!NOTE]
> V tuto chvíli odepřít jediný způsob, jakým můžete přidat vlastní přiřazení je s využitím Azure podrobné plány. Další informace najdete v tématu [chránit nové prostředky podle zámky prostředků Azure plány](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="deny-assignment-properties"></a>Zamítnout přiřazení vlastnosti

 Přiřazení Odepřít má následující vlastnosti:

> [!div class="mx-tableFixed"]
> | Vlastnost | Požaduje se | Typ | Popis |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ano | String | Zobrazovaný název přiřazení odepřít. Názvy musí být jedinečné pro daný obor. |
> | `Description` | Ne | String | Popis přiřazení odepřít. |
> | `Permissions.Actions` | Nejméně jedna akce nebo jeden DataActions | Řetězec] | Pole řetězců, které určují operace správy, ke kterým zablokuje přiřazení odepřít přístup. |
> | `Permissions.NotActions` | Ne | Řetězec] | Pole řetězců, které určují operace správy, které chcete vyloučit z přiřazení odepřít. |
> | `Permissions.DataActions` | Nejméně jedna akce nebo jeden DataActions | Řetězec] | Pole řetězců, které určují operace dat, ke kterým zablokuje přiřazení odepřít přístup. |
> | `Permissions.NotDataActions` | Ne | Řetězec] | Pole řetězců, které určují operace s daty chcete vyloučit z přiřazení odepřít. |
> | `Scope` | Ne | String | Řetězec, který určuje obor, vztahující se k přiřazení odepřít. |
> | `DoNotApplyToChildScopes` | Ne | Boolean | Určuje, zda Odepřít přiřazení se vztahuje na podřízené obory. Výchozí hodnota je false. |
> | `Principals[i].Id` | Ano | Řetězec] | Pole instanční objekt Azure AD ID (uživatele, skupiny, instanční objekt nebo spravovanou identitu), u kterých bude použito přiřazení odepřít. Nastavit na prázdný identifikátor GUID `00000000-0000-0000-0000-000000000000` představující všechny objekty zabezpečení. |
> | `Principals[i].Type` | Ne | Řetězec] | Pole typů objekt reprezentovaný .id objekty zabezpečení [i]. Nastavte na `SystemDefined` představující všechny objekty zabezpečení. |
> | `ExcludePrincipals[i].Id` | Ne | Řetězec] | Pole instanční objekt Azure AD ID (uživatele, skupiny, instanční objekt nebo spravovanou identitu), do kterých se přiřazení odepřít nevztahuje. |
> | `ExcludePrincipals[i].Type` | Ne | Řetězec] | Pole typů objekt reprezentovaný .id ExcludePrincipals [i]. |
> | `IsSystemProtected` | Ne | Boolean | Určuje, zda to zamítnout přiřazení vytvořil Azure a nelze upravovat ani odstranit. V současné době všechny odepřít systému chráněné jsou odevzdané úkoly. |

## <a name="system-defined-principal"></a>Objekt zabezpečení definované v systému

Pro podporu zamítnout přiřazení, **systémem definovaná hlavní** je zavedený. Tento objekt zabezpečení představuje uživatele, skupiny, instančních objektů a spravovaných identit v adresáři Azure AD. Pokud je ID objektu zabezpečení žádný identifikátor GUID `00000000-0000-0000-0000-000000000000` a typ objektu zabezpečení je `SystemDefined`, objekt zabezpečení představuje všechny objekty zabezpečení. `SystemDefined` je možné kombinovat s `ExcludePrincipals` pro všechny objekty zabezpečení, s výjimkou někteří uživatelé odepření. `SystemDefined` má následující omezení:

- Lze použít pouze ve `Principals` a nelze jej použít v `ExcludePrincipals`.
- `Principals[i].Type` musí být nastaveno na `SystemDefined`.

## <a name="next-steps"></a>Další postup

* [Zobrazení zamítnout přiřazení pro prostředky Azure pomocí webu Azure portal](deny-assignments-portal.md)
* [Pochopení definic rolí pro prostředky Azure](role-definitions.md)
