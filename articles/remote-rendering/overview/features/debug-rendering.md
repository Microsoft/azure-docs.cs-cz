---
title: Ladění vykreslování
description: Přehled efektů vykreslování ladění na straně serveru
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: 675f8d988e64ed7b556f154f681ccb53ed1000c6
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394290"
---
# <a name="debug-rendering"></a>Ladění vykreslování

Rozhraní API pro vykreslování ladicích barev poskytuje řadu globálních možností pro změnu vykreslování na straně serveru s různými efekty ladění.

## <a name="available-debug-rendering-effects"></a>Dostupné efekty ladění vykreslování

|Nastavení                          | Účinek                               |
|---------------------------------|:-------------------------------------|
|Čítač rámů                    | Vykreslí překrytí textu do levého horního rohu rámečku. Text zobrazuje aktuální ID rámečku na straně serveru, které se průběžně zintáčete při vykreslování. |
|Počet polygonů                    | Vykreslí překrytí textu do levého horního rohu rámečku. Text zobrazuje aktuálně vykreslené množství polygonů, stejnou hodnotu, jakou [dotazovaly dotazy na straně serveru.](performance-queries.md)| 
|Skeletové                        | Pokud je tato možnost povolena, bude veškerá geometrie objektu načtená na serveru vykreslena v režimu drátového modelu. V tomto režimu budou rastrovány pouze hrany polygonů. |

Následující kód umožňuje tyto efekty ladění:

``` cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

![Ladění vykreslování](./media/debug-rendering.png)

> [!NOTE]
> Všechny efekty vykreslení ladění jsou globální nastavení, která ovlivňují celý snímek.

## <a name="use-cases"></a>Případy použití

Rozhraní API pro vykreslování ladivých dat je určeno pro jednoduché úlohy ladění, jako je ověření, že připojení služby je ve skutečnosti správně spuštěno. Volby vykreslování textu přímo ovlivňují snímky videa vysílané dolů. Povolením ověříte, zda jsou přijaty nové snímky a video dekódováno správně.

Nicméně, poskytnuté účinky nedávají žádné podrobné introspekce do stavu služby. Pro tento případ použití se doporučují [dotazy na výkon na straně serveru.](performance-queries.md)

## <a name="performance-considerations"></a>Otázky výkonu

* Povolení překrytí textu vznikne jen málo nebo žádné režie výkonu.
* Navíc povolení překrytí vznikne netriviální výkon režie, i když se může lišit v závislosti na scéně. U složitých scén může tento režim způsobit pokles snímkové frekvence pod cíl 60 Hz.

## <a name="next-steps"></a>Další kroky

* [Režimy vykreslování](../../concepts/rendering-modes.md)
* [Dotazy na výkon na straně serveru](performance-queries.md)
