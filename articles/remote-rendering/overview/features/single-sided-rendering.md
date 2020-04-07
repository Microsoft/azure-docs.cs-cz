---
title: Jednostranné vykreslování
description: Popisuje jednostranná nastavení vykreslování a případy použití.
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682010"
---
# <a name="single-sided-rendering"></a>Jednostranné vykreslování

Většina rendererů používá k lepšímu výkonu [utracení zadní části](https://en.wikipedia.org/wiki/Back-face_culling) obličeje. Když jsou však ok rozříznuty [rovinami řezu](cut-planes.md), uživatelé se často dívají na zadní stranu trojúhelníků. Pokud jsou tyto trojúhelníky vyřazeny, výsledek nevypadá přesvědčivě.

Způsob, jak spolehlivě zabránit tomuto problému, je vykreslit trojúhelníky *oboustranné*. Jako nepoužíváte back-face utracení má vliv na výkon, ve výchozím nastavení Azure vzdálené vykreslování pouze přepne na oboustranné vykreslování pro mše, které se protínají s rovinou řezu.

Jednostranné nastavení *vykreslování* umožňuje přizpůsobit toto chování.

> [!CAUTION]
> Jednostranné nastavení vykreslování je experimentální funkce. To může dostat odstraněny znovu v budoucnu. Prosím, neměňte výchozí nastavení, pokud to opravdu řeší kritický problém ve vaší aplikaci.

## <a name="prerequisites"></a>Požadavky

Jednostranné nastavení vykreslování má vliv pouze na `opaqueMaterialDefaultSidedness` mříží, která byla [převedena](../../how-tos/conversion/configure-model-conversion.md) s možností nastavenou na `SingleSided`. Ve výchozím nastavení je `DoubleSided`tato možnost nastavena na hodnotu .

## <a name="single-sided-rendering-setting"></a>Jednostranné nastavení vykreslování

Existují tři různé režimy:

**Normální:** V tomto režimu jsou mísy vždy vykresleny při převodu. To znamená, že sítě `opaqueMaterialDefaultSidedness` převedené s nastavenou na `SingleSided` se vždy vykreslí s povoleným utracením zadní plochy, i když protínají rovinu řezu.

**Dynamické oboustranné siding:** V tomto režimu, když rovina řezu protíná síť, je automaticky přepnuta na oboustranné vykreslování. Tento režim je výchozím režimem.

**AlwaysDoubleSided:** Vynutí, aby byla vždy oboustranná vykreslovaná veškerá jednostranná geometrie. Tento režim je většinou vystaven, takže můžete snadno porovnat dopad na výkon mezi jednostranné a oboustranné vykreslování.

Změna nastavení jednostranného vykreslování lze provést následujícím způsobem:

``` cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

## <a name="next-steps"></a>Další kroky

* [Řezané roviny](cut-planes.md)
* [Konfigurace převodu modelu](../../how-tos/conversion/configure-model-conversion.md)
