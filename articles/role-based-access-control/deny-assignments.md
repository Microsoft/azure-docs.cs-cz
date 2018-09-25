---
title: Vysvětlení zamítnout přiřazení v Azure RBAC | Dokumentace Microsoftu
description: Další informace o zamítnutí přiřazení v řízení přístupu na základě role (RBAC) pro prostředky v Azure.
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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 8ef3a2ec44c5eff80d3a50a6c56805667e164ba8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980164"
---
# <a name="understand-deny-assignments"></a>Vysvětlení zamítnout přiřazení

Přiřazení role, podobně jako *zamítnout přiřazení* akce odepřít vazby skupinu pro uživatele, skupinu nebo instanční objekt v určitém rozsahu pro účely odepření přístupu. Odepřít přiřazení můžete také vyloučit objekty zabezpečení a zabránit dědění za účelem podřízené obory, které se liší od přiřazení rolí. V současné době zamítnout přiřazení **jen pro čtení** a lze nastavit pouze v Azure. Tento článek popisuje, jak zakázat přiřazení jsou definovány.

## <a name="deny-assignment-properties"></a>Zamítnout přiřazení vlastnosti

 Přiřazení Odepřít má následující vlastnosti:

> [!div class="mx-tableFixed"]
> | Vlastnost | Požaduje se | Typ | Popis |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ano | Řetězec | Zobrazovaný název přiřazení odepřít. Názvy musí být jedinečné pro daný obor. |
> | `Description` | Ne | Řetězec | Popis přiřazení odepřít. |
> | `Permissions.Actions` | Nejméně jedna akce nebo jeden DataActions | Řetězec] | Pole řetězců, které určují operace správy, ke kterým zablokuje přiřazení odepřít přístup. |
> | `Permissions.NotActions` | Ne | Řetězec] | Pole řetězců, které určují operace správy, které chcete vyloučit z přiřazení odepřít. |
> | `Permissions.DataActions` | Nejméně jedna akce nebo jeden DataActions | Řetězec] | Pole řetězců, které určují operace dat, ke kterým zablokuje přiřazení odepřít přístup. |
> | `Permissions.NotDataActions` | Ne | Řetězec] | Pole řetězců, které určují operace s daty chcete vyloučit z přiřazení odepřít. |
> | `Scope` | Ne | Řetězec | Řetězec, který určuje obor, vztahující se k přiřazení odepřít. |
> | `DoNotApplyToChildScopes` | Ne | Logická hodnota | Určuje, zda Odepřít přiřazení se vztahuje na podřízené obory. Výchozí hodnota je false. |
> | `Principals[i].Id` | Ano | Řetězec] | Pole instanční objekt Azure AD ID (uživatele, skupinu nebo instanční objekt služby), u kterých bude použito přiřazení odepřít. Nastavit na prázdný identifikátor GUID `00000000-0000-0000-0000-000000000000` představující všem uživatelům. |
> | `Principals[i].Type` | Ne | Řetězec] | Pole typů objekt reprezentovaný .id objekty zabezpečení [i]. Nastavte na `Everyone` představující všem uživatelům. |
> | `ExcludePrincipals[i].Id` | Ne | Řetězec] | Pole instanční objekt Azure AD ID (uživatele, skupinu nebo instanční objekt služby), do kterých se přiřazení odepřít nevztahuje. |
> | `ExcludePrincipals[i].Type` | Ne | Řetězec] | Pole typů objekt reprezentovaný .id ExcludePrincipals [i]. |
> | `IsSystemProtected` | Ne | Logická hodnota | Určuje, zda to zamítnout přiřazení vytvořil Azure a nelze upravovat ani odstranit. V současné době všechny odepřít systému chráněné jsou odevzdané úkoly. |

## <a name="everyone-principal"></a>Všichni instančního objektu

Pro podporu zamítnout přiřazení, všichni instančního objektu je zavedený. Všichni hlavní představuje uživatele, skupiny nebo instanční objekty v adresáři Azure AD. Pokud je ID objektu zabezpečení žádný identifikátor GUID `00000000-0000-0000-0000-000000000000` a typ objektu zabezpečení je `Everyone`, objekt zabezpečení představuje všem uživatelům. Každý instanční objekt je možné kombinovat s `ExcludePrincipals` odepřít všem uživatelům kromě některých uživatelů. Hlavní všichni mají následující omezení:

- Lze použít pouze ve `Principals` a nelze jej použít v `ExcludePrincipals`.
- `Principals[i].Type` musí být nastaveno na `Everyone`.

## <a name="next-steps"></a>Další postup

* [Seznam zamítnout přiřazení pomocí RBAC a rozhraní REST API](deny-assignments-rest.md)
* [Vysvětlení definice rolí](role-definitions.md)
