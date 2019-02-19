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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 53716fa343df25026dcc668ed8483673d934d1ad
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56339120"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Vysvětlení zamítnout přiřazení pro prostředky Azure

Přiřazení role, podobně jako *zamítnout přiřazení* akce Odepřít bude k obrazci skupinu pro uživatele, skupinu nebo instanční objekt v určitém rozsahu pro účely odepření přístupu. Zamítnout přiřazení zablokovat uživatelům provádět akce konkrétních prostředků Azure i v případě přiřazení role uděluje přístup. Některé prostředek, který poskytovatelů služeb v Azure teď obsahují zamítnout přiřazení. V současné době zamítnout přiřazení **jen pro čtení** a lze nastavit pouze společností Microsoft.

V některých ohledech odeprete přiřazení se liší od přiřazení rolí. Zamítnout přiřazení můžete vyloučit objekty zabezpečení a zabránit dědění za účelem podřízených oborech. Zamítnout přiřazení platí také pro [klasický správce předplatného](rbac-and-directory-admin-roles.md) přiřazení.

Tento článek popisuje, jak zakázat přiřazení jsou definovány.

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
> | `DoNotApplyToChildScopes` | Ne | Logická hodnota | Určuje, zda Odepřít přiřazení se vztahuje na podřízené obory. Výchozí hodnota je false. |
> | `Principals[i].Id` | Ano | Řetězec] | Pole instanční objekt Azure AD ID (uživatele, skupiny, instanční objekt nebo spravovanou identitu), u kterých bude použito přiřazení odepřít. Nastavit na prázdný identifikátor GUID `00000000-0000-0000-0000-000000000000` představující všechny objekty zabezpečení. |
> | `Principals[i].Type` | Ne | Řetězec] | Pole typů objekt reprezentovaný .id objekty zabezpečení [i]. Nastavte na `SystemDefined` představující všechny objekty zabezpečení. |
> | `ExcludePrincipals[i].Id` | Ne | Řetězec] | Pole instanční objekt Azure AD ID (uživatele, skupiny, instanční objekt nebo spravovanou identitu), do kterých se přiřazení odepřít nevztahuje. |
> | `ExcludePrincipals[i].Type` | Ne | Řetězec] | Pole typů objekt reprezentovaný .id ExcludePrincipals [i]. |
> | `IsSystemProtected` | Ne | Logická hodnota | Určuje, zda to zamítnout přiřazení vytvořil Azure a nelze upravovat ani odstranit. V současné době všechny odepřít systému chráněné jsou odevzdané úkoly. |

## <a name="system-defined-principal"></a>Objekt zabezpečení definované v systému

Pro podporu zamítnout přiřazení, **systémem definovaná hlavní** je zavedený. Tento objekt zabezpečení představuje uživatele, skupiny, instančních objektů a spravovaných identit v adresáři Azure AD. Pokud je ID objektu zabezpečení žádný identifikátor GUID `00000000-0000-0000-0000-000000000000` a typ objektu zabezpečení je `SystemDefined`, objekt zabezpečení představuje všechny objekty zabezpečení. `SystemDefined` je možné kombinovat s `ExcludePrincipals` pro všechny objekty zabezpečení, s výjimkou někteří uživatelé odepření. `SystemDefined` má následující omezení:

- Lze použít pouze ve `Principals` a nelze jej použít v `ExcludePrincipals`.
- `Principals[i].Type` musí být nastaveno na `SystemDefined`.

## <a name="next-steps"></a>Další postup

* [Seznam zamítnout přiřazení pro prostředky Azure pomocí rozhraní REST API](deny-assignments-rest.md)
* [Pochopení definic rolí pro prostředky Azure](role-definitions.md)
