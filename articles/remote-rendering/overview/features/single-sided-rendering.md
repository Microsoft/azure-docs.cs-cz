---
title: Jednostranné vykreslování
description: Popisuje nastavení jednoduchého vykreslování a případy použití.
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: fea9deae3948b36732b5ea5203fceea6bec07fb9
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594074"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided"::: vykreslování

Většina zobrazovacích ploch používá ke zvýšení výkonu [odstranení zadní plochy](https://en.wikipedia.org/wiki/Back-face_culling) . Pokud jsou ale sítě vyjmuté z oblasti [vyjmuté plochy](cut-planes.md), budou se uživatelé často zobrazovat na zadní straně trojúhelníků. Pokud jsou tyto trojúhelníky poraženy, výsledek nevypadá jako přesvědčivý.

Způsob, jak tento problém spolehlivě zabránit, je vykreslovat trojúhelníky *dvojitě na sebe*. Pokud se nepoužívá odstraněné odložení na pozadí, je ve výchozím nastavení pro vzdálené vykreslování Azure přepnuto jenom na dvojité vykreslování pro sítě, které se protínají s vyjmutou rovinou.

Nastavení *:::no-loc text="single-sided"::: vykreslování* umožňuje přizpůsobit toto chování.

> [!CAUTION]
> :::no-loc text="single-sided":::Nastavení vykreslování je experimentální funkce. V budoucnu se může znovu odebrat. Neměňte prosím výchozí nastavení, pokud skutečně neřeší kritickou chybu ve vaší aplikaci.

## <a name="prerequisites"></a>Požadavky

:::no-loc text="single-sided":::Nastavení vykreslení má pouze efekt pro sítě, které byly [převedeny](../../how-tos/conversion/configure-model-conversion.md) s `opaqueMaterialDefaultSidedness` možností nastavenou na `SingleSided` . Ve výchozím nastavení je tato možnost nastavena na hodnotu `DoubleSided` .

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided"::: nastavení vykreslování

Existují tři různé režimy:

**Normální:** V tomto režimu jsou sítě vždy vykresleny při jejich převodu. To znamená, že sítě převedené pomocí `opaqueMaterialDefaultSidedness` nastavené na `SingleSided` se vždycky vykreslí s povoleným odstraněním na pozadí, i když se překrývají vyjmuté plochy.

**DynamicDoubleSiding:** V tomto režimu, když vyjmutá rovina protíná síť, je automaticky přepnuta na dvojité vykreslování. Tento režim je výchozím režimem.

**AlwaysDoubleSided:** Vynutí, aby se všechny jednoduché geometrie na jednom straně vykreslily dvakrát. Tento režim je většinou vystavený, takže můžete snadno porovnat dopad na výkon :::no-loc text="single-sided"::: a :::no-loc text="double-sided"::: vykreslování.

Změna :::no-loc text="single-sided"::: nastavení vykreslování může probíhat takto:

```cs
void ChangeSingleSidedRendering(RenderingSession session)
{
    SingleSidedSettings settings = session.Connection.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<RenderingSession> session)
{
    ApiHandle<SingleSidedSettings> settings = session->Connection()->GetSingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->SetMode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->SetMode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Vlastnost C# RenderingConnection. SingleSidedSettings](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.singlesidedsettings)
* [C++ RenderingConnection:: SingleSidedSettings ()](/cpp/api/remote-rendering/renderingconnection#singlesidedsettings)

## <a name="next-steps"></a>Další kroky

* [Řezy roviny](cut-planes.md)
* [Konfigurace převodu modelu](../../how-tos/conversion/configure-model-conversion.md)