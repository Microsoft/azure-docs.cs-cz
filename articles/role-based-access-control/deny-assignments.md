---
title: Principy odepření přiřazení pro prostředky Azure
description: Další informace o odepřít přiřazení v řízení přístupu na základě rolí (RBAC) pro prostředky Azure.
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
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: db249ccde1026cd468a1c30942891119482697ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372486"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Principy odepření přiřazení pro prostředky Azure

Podobně jako přiřazení role, *odepřít přiřazení* připojí sadu odepřít akce na uživatele, skupiny nebo instanční objekt v určitém oboru za účelem odepření přístupu. Odepřít přiřazení blokovat uživatelům provádět konkrétní akce prostředků Azure i v případě, že přiřazení role jim uděluje přístup.

Tento článek popisuje, jak jsou definovány odepřít přiřazení.

## <a name="how-deny-assignments-are-created"></a>Jak se vytvářejí přiřazení odepření

Odepřít přiřazení jsou vytvořeny a spravovány Azure k ochraně prostředků. Azure Blueprints a spravované aplikace Azure používají k ochraně prostředků spravovaných systémem přiřazení k odepření. Azure Blueprints a spravované aplikace Azure jsou jediný způsob, jak odepřít přiřazení lze vytvořit. Nelze přímo vytvořit vlastní přiřazení odepřít. Další informace o tom, jak plány používají odepřít přiřazení k uzamčení prostředků, najdete [v tématu Principy uzamčení prostředků v Azure Blueprints](../governance/blueprints/concepts/resource-locking.md).

> [!NOTE]
> Nelze přímo vytvořit vlastní přiřazení odepřít.

## <a name="compare-role-assignments-and-deny-assignments"></a>Porovnání přiřazení rolí a odepření přiřazení

Odepřít přiřazení následovat podobný vzor jako přiřazení rolí, ale také mají některé rozdíly.

| Schopnost | Přiřazení role | Odepřít přiřazení |
| --- | --- | --- |
| Udělení přístupu | :heavy_check_mark: |  |
| Odepření přístupu |  | :heavy_check_mark: |
| Lze přímo vytvořit | :heavy_check_mark: |  |
| Použít v oboru | :heavy_check_mark: | :heavy_check_mark: |
| Vyloučit objekty zabezpečení |  | :heavy_check_mark: |
| Zabránit dědičnosti podřízených oborů |  | :heavy_check_mark: |
| Použití [u klasických](rbac-and-directory-admin-roles.md) přiřazení správce předplatného |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Odepřít vlastnosti přiřazení

 Přiřazení odepření má následující vlastnosti:

> [!div class="mx-tableFixed"]
> | Vlastnost | Požaduje se | Typ | Popis |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ano | Řetězec | Zobrazovaný název odepřít přiřazení. Názvy musí být jedinečné pro daný obor. |
> | `Description` | Ne | Řetězec | Popis odepřít přiřazení. |
> | `Permissions.Actions` | Alespoň jedna akce nebo jedna dataakce | Řetězec[] | Pole řetězců, které určují operace správy, ke kterým odepřít přiřazení blokuje přístup. |
> | `Permissions.NotActions` | Ne | Řetězec[] | Pole řetězců, které určují operace správy vyloučit z přiřazení odepřít. |
> | `Permissions.DataActions` | Alespoň jedna akce nebo jedna dataakce | Řetězec[] | Pole řetězců, které určují datové operace, ke kterým odepřít přiřazení blokuje přístup. |
> | `Permissions.NotDataActions` | Ne | Řetězec[] | Pole řetězců, které určují operace dat vyloučit z přiřazení odepřít. |
> | `Scope` | Ne | Řetězec | Řetězec, který určuje obor, na který se vztahuje přiřazení odepřít. |
> | `DoNotApplyToChildScopes` | Ne | Logická hodnota | Určuje, zda se přiřazení odepření vztahuje na podřízené obory. Výchozí hodnota je false. |
> | `Principals[i].Id` | Ano | Řetězec[] | Pole ID objektu primárního objektu Azure AD (uživatel, skupina, instanční objekt nebo spravovaná identita), na které se vztahuje přiřazení odepřít. Nastavte na prázdný `00000000-0000-0000-0000-000000000000` identifikátor GUID, který představuje všechny objekty zabezpečení. |
> | `Principals[i].Type` | Ne | Řetězec[] | Pole typů objektů reprezentovaných Principals[i].Id. Nastavte tak, aby `SystemDefined` představovala všechny objekty zabezpečení. |
> | `ExcludePrincipals[i].Id` | Ne | Řetězec[] | Pole ID objektu primárního objektu Azure AD (uživatel, skupina, instanční objekt nebo spravovaná identita), na které se nevztahuje přiřazení odepřít. |
> | `ExcludePrincipals[i].Type` | Ne | Řetězec[] | Pole typů objektů reprezentované ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Ne | Logická hodnota | Určuje, zda toto přiřazení odepření bylo vytvořeno azure a nelze upravovat nebo odstraňovat. V současné době jsou všechna přiřazení zamítnutí chráněna systémem. |

## <a name="the-all-principals-principal"></a>Všechny principy hlavní

Pro podporu odepřít přiřazení, systémem definované jistiny s názvem *Všechny objekty zabezpečení* byla zavedena. Tento objekt zabezpečení představuje všechny uživatele, skupiny, instanční objekty a spravované identity v adresáři Azure AD. Pokud id jistiny je `00000000-0000-0000-0000-000000000000` nula GUID `SystemDefined`a hlavní typ je , hlavní představuje všechny objekty zabezpečení. Ve výstupu Prostředí Azure PowerShell všechny objekty zabezpečení vypadá takto:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Všechny objekty zabezpečení lze `ExcludePrincipals` kombinovat s odepřít všechny objekty zabezpečení s výjimkou některých uživatelů. Všechny objekty zabezpečení má následující omezení:

- Lze použít pouze `Principals` v aplikaci `ExcludePrincipals`a nelze ji použít v aplikaci .
- `Principals[i].Type`musí být `SystemDefined`nastavena na .

## <a name="next-steps"></a>Další kroky

* [Kurz: Ochrana nových prostředků pomocí zámků prostředků Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md)
* [Seznam odepřít přiřazení pro prostředky Azure pomocí portálu Azure](deny-assignments-portal.md)
