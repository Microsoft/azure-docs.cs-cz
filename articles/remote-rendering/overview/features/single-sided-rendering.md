---
title: Jednostranné vykreslování
description: Popisuje nastavení jednoduchého vykreslování a případy použití.
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682010"
---
# <a name="single-sided-rendering"></a>Jednostranné vykreslování

Většina zobrazovacích ploch používá ke zvýšení výkonu [odstranení zadní plochy](https://en.wikipedia.org/wiki/Back-face_culling) . Pokud jsou ale sítě vyjmuté z oblasti [vyjmuté plochy](cut-planes.md), budou se uživatelé často zobrazovat na zadní straně trojúhelníků. Pokud jsou tyto trojúhelníky poraženy, výsledek nevypadá jako přesvědčivý.

Způsob, jak tento problém spolehlivě zabránit, je vykreslovat trojúhelníky *dvojitě na sebe*. Pokud se nepoužívá odstraněné odložení na pozadí, je ve výchozím nastavení pro vzdálené vykreslování Azure přepnuto jenom na dvojité vykreslování pro sítě, které se protínají s vyjmutou rovinou.

Nastavení *jednoduchého vykreslování* umožňuje přizpůsobit toto chování.

> [!CAUTION]
> Nastavení jednoduchého vykreslování je experimentální funkce. V budoucnu se může znovu odebrat. Neměňte prosím výchozí nastavení, pokud skutečně neřeší kritickou chybu ve vaší aplikaci.

## <a name="prerequisites"></a>Požadavky

Nastavení vykreslování s jednou stranou má pouze efekt pro sítě, které byly [převedeny](../../how-tos/conversion/configure-model-conversion.md) s `opaqueMaterialDefaultSidedness` možností nastavenou na `SingleSided`. Ve výchozím nastavení je tato možnost nastavena `DoubleSided`na hodnotu.

## <a name="single-sided-rendering-setting"></a>Nastavení vykreslování s jednou stranou

Existují tři různé režimy:

**Normální:** V tomto režimu jsou sítě vždy vykresleny při jejich převodu. To znamená, že sítě `opaqueMaterialDefaultSidedness` převedené `SingleSided` pomocí nastavené na se vždycky vykreslí s povoleným odstraněním na pozadí, i když se překrývají vyjmuté plochy.

**DynamicDoubleSiding:** V tomto režimu, když vyjmutá rovina protíná síť, je automaticky přepnuta na dvojité vykreslování. Tento režim je výchozím režimem.

**AlwaysDoubleSided:** Vynutí, aby se všechny jednoduché geometrie na jednom straně vykreslily dvakrát. Tento režim je většinou vystavený, takže můžete snadno porovnat dopad na výkon mezi jednostranným a dvojitým vykreslováním.

Změna nastavení s jedním jednostranným vykreslováním se dá udělat takto:

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

* [Řezy roviny](cut-planes.md)
* [Konfigurace převodu modelu](../../how-tos/conversion/configure-model-conversion.md)
