---
title: Pochopení přiřazení Azure Deny – Azure RBAC
description: Přečtěte si o přiřazeních odmítnutí Azure v řízení přístupu na základě role Azure (Azure RBAC).
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
ms.openlocfilehash: a5f17f009caa9306631debf511f2c890f8f2a450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "82733767"
---
# <a name="understand-azure-deny-assignments"></a>Pochopení přiřazení Azure Deny

Podobně jako u přiřazení role *přiřazení zamítnutí* připojuje sadu akcí Odepřít pro uživatele, skupinu nebo instanční objekt v konkrétním oboru pro účely odepření přístupu. Odmítnutí přiřazení zablokuje uživatelům, aby prováděli konkrétní akce prostředku Azure i v případě, že jim přiřazením role udělí přístup.

Tento článek popisuje, jak jsou definována přiřazení zamítnutí.

## <a name="how-deny-assignments-are-created"></a>Způsob vytvoření přiřazení zamítnutí

Přiřazení zamítnutí jsou vytvářena a spravována službou Azure za účelem ochrany prostředků. Plány Azure a spravované aplikace Azure používají přiřazení odepřít k ochraně prostředků spravovaných systémem. Plány Azure a spravované aplikace Azure jsou jediný způsob, jakým je možné vytvořit přiřazení odepřít. Nemůžete přímo vytvořit vlastní přiřazení zamítnutí. Další informace o tom, jak plány používají přiřazení Odepřít pro zamčení prostředků, najdete v tématu [vysvětlení uzamykání prostředků v Azure modrotisky](../governance/blueprints/concepts/resource-locking.md).

> [!NOTE]
> Nemůžete přímo vytvořit vlastní přiřazení zamítnutí.

## <a name="compare-role-assignments-and-deny-assignments"></a>Porovnání přiřazení rolí a zamítnutí přiřazení

Přiřazení odepřít následují podobný vzor jako přiřazení rolí, ale také některé rozdíly.

| Schopnost | Přiřazení role | Odepřít přiřazení |
| --- | --- | --- |
| Udělení přístupu | :heavy_check_mark: |  |
| Odepření přístupu |  | :heavy_check_mark: |
| Lze vytvořit přímo | :heavy_check_mark: |  |
| Použít v oboru | :heavy_check_mark: | :heavy_check_mark: |
| Vyloučit objekty zabezpečení |  | :heavy_check_mark: |
| Zabránit dědění do podřízených oborů |  | :heavy_check_mark: |
| Použít na přiřazení [klasických správců předplatného](rbac-and-directory-admin-roles.md) |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Vlastnosti přiřazení zamítnutí

 Přiřazení zamítnutí má následující vlastnosti:

> [!div class="mx-tableFixed"]
> | Vlastnost | Požaduje se | Typ | Popis |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ano | Řetězec | Zobrazovaný název přiřazení zamítnutí. Názvy musí být pro daný obor jedinečné. |
> | `Description` | No | Řetězec | Popis přiřazení zamítnutí. |
> | `Permissions.Actions` | Aspoň jedna akce nebo jedna akce. | Řetězec [] | Pole řetězců, které určují operace správy, na které přiřazení zamítnutí blokuje přístup. |
> | `Permissions.NotActions` | Ne | Řetězec [] | Pole řetězců, které určují operace správy, které mají být vyloučeny z přiřazení zamítnutí. |
> | `Permissions.DataActions` | Aspoň jedna akce nebo jedna akce. | Řetězec [] | Pole řetězců, které určují datové operace, na které přiřazení zamítnutí blokuje přístup. |
> | `Permissions.NotDataActions` | Ne | Řetězec [] | Pole řetězců, které určují datové operace, které mají být vyloučeny z přiřazení zamítnutí. |
> | `Scope` | No | Řetězec | Řetězec, který určuje rozsah, pro který se přiřazení odepřít. |
> | `DoNotApplyToChildScopes` | Ne | Logická hodnota | Určuje, zda se přiřazení odepřít vztahuje na podřízené obory. Výchozí hodnota je false. |
> | `Principals[i].Id` | Ano | Řetězec [] | Pole ID objektu zabezpečení služby Azure AD (uživatel, skupina, instanční objekt nebo spravovaná identita), na které se vztahuje přiřazení zamítnutí. Nastavte na prázdný identifikátor GUID `00000000-0000-0000-0000-000000000000` , který bude představovat všechny objekty zabezpečení. |
> | `Principals[i].Type` | Ne | Řetězec [] | Pole typů objektů reprezentovaných objekty zabezpečení [i]. ID. nastaveno na hodnotu `SystemDefined` , která představuje všechny objekty zabezpečení. |
> | `ExcludePrincipals[i].Id` | Ne | Řetězec [] | Pole ID objektu zabezpečení služby Azure AD (uživatel, skupina, instanční objekt nebo spravovaná identita), na které se nevztahují přiřazení zamítnutí. |
> | `ExcludePrincipals[i].Type` | Ne | Řetězec [] | Pole typů objektů reprezentovaných ExcludePrincipals [i]. ID. |
> | `IsSystemProtected` | Ne | Logická hodnota | Určuje, jestli toto přiřazení zamítnutí vytvořila Azure a nedá se upravit ani odstranit. V současné době jsou všechna přiřazení zamítnutá systémem chráněná. |

## <a name="the-all-principals-principal"></a>Všechny objekty zabezpečení

Aby bylo možné podporovat přiřazení zamítnutí, bylo zavedeno systémem definovaný objekt zabezpečení nazvaný *všechny objekty zabezpečení* . Tento objekt zabezpečení představuje všechny uživatele, skupiny, instanční objekty a spravované identity v adresáři Azure AD. Pokud je ID objektu zabezpečení nula GUID `00000000-0000-0000-0000-000000000000` a typ objektu zabezpečení je `SystemDefined` , představuje objekt zabezpečení všechny objekty zabezpečení. V Azure PowerShellovém výstupu budou všechny objekty zabezpečení vypadat takto:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Všechny objekty zabezpečení mohou být kombinovány s `ExcludePrincipals` cílem Odepřít všechny objekty zabezpečení s výjimkou uživatelů. Všechny objekty zabezpečení mají následující omezení:

- Dá se použít jenom v `Principals` a nedá se použít v `ExcludePrincipals` .
- `Principals[i].Type` musí být nastaven na hodnotu `SystemDefined` .

## <a name="next-steps"></a>Další kroky

* [Kurz: ochrana nových prostředků pomocí zámků prostředků Azure modrotisky](../governance/blueprints/tutorials/protect-new-resources.md)
* [Výpis přiřazení odmítnutí Azure pomocí Azure Portal](deny-assignments-portal.md)
