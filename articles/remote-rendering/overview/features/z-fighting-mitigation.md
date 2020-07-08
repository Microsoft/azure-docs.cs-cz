---
title: Zmírnění Z-fightingu
description: Popisuje techniky pro zmírnění rizikových artefaktů z-boje.
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: f4c49be5f5a0f2c89831891dc2640b64fee9fc44
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022414"
---
# <a name="z-fighting-mitigation"></a>Zmírnění Z-fightingu

Pokud se dva povrchy překrývají, není jasné, které z nich by měly být vykresleny nad druhou. Výsledek se dokonce liší podle pixelu, což vede k artefaktům závislým na zobrazení kamery. V důsledku toho se při každém přesunu kamery nebo sítě tyto vzory blikání pozorují. Tento artefakt se nazývá *z-boje*. U aplikací AR a VR je problém posílený, protože zařízení připojená k hlavním místům jsou přirozeně vždycky přesunuta. K tomu, aby se zabránilo prohlížeči v boji proti zmírnění, je k dispozici ve vzdáleném vykreslování Azure.

## <a name="z-fighting-mitigation-modes"></a>Režimy Z-boj proti zmírnění

|Status                        | Výsledek                               |
|---------------------------------|:-------------------------------------|
|Normální z-boj               |![Z-boj](./media/zfighting-0.png)|
|Z-boj povoleno zmírnění    |![Z-boj](./media/zfighting-1.png)|
|Zvýrazňování šachovnice povoleno|![Z-boj](./media/zfighting-2.png)|

Následující kód povoluje zmírnění omezení z boje:

```cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = *session->Actions()->ZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->Enabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->Highlighting(highlight);
}
```


> [!NOTE]
> Z-boje proti zmírnění je globální nastavení, které ovlivňuje všechny vygenerované sítě.

## <a name="reasons-for-z-fighting"></a>Důvody pro z-boje

Z-boje se děje hlavně ze dvou důvodů:

1. Když jsou povrchy od kamery příliš daleko, přesnost hodnot jejich hloubky se sníží a hodnoty se stanou nerozlišené.
1. Když se fyzicky překrývají povrchy v mřížce

K prvnímu problému může dojít vždy a je obtížné ho eliminovat. Pokud k tomu dojde ve vaší aplikaci, ujistěte se, že poměr vzdálenosti *blízko roviny* až po *Velká rovině* je jako praktický. Například poblíž roviny na dálku 0,01 a daleko až 1000 se tento problém bude nacházet mnohem dříve, než má skoro rovina v 0,1 a na nejvyšší rovině ve vzdálenosti 20.

Druhý problém je indikátorem chybně vytvořeného obsahu. V reálném světě se dva objekty nemůžou nacházet na stejném místě. V závislosti na aplikaci mohou uživatelé chtít zjistit, zda existují překrývající se povrchy a kde jsou. Například scéna CAD budovy, která je základem pro konstrukci reálného světa, by neměla obsahovat fyzicky nereálné průřezy povrchu. Aby bylo možné vizuální kontrolu, je k dispozici režim zvýrazňování, který jako animovaný šachovnicový vzor zobrazuje potenciál z hlediska odboje.

## <a name="limitations"></a>Omezení

K dispozici je nejlepší úsilí z hlediska zmírnění omezení z hlediska boje. Není zaručeno, že odebere všechny z boje. Také bude automaticky preferovat jednu plochu v jiném. Proto pokud máte povrchy, které jsou příliš blízko sobě navzájem, může dojít k tomu, že "špatný" povrch skončil nahoru. Běžný případ problému je, když se na Surface používá text a další decals. S povoleným omezením na úrovni z-boje by tyto podrobnosti mohly snadno pouze zobrazit.

## <a name="performance-considerations"></a>Otázky výkonu

* Povolením z boje proti zmírnění rizik dojde k nedostatečnému výkonu.
* Kromě povolení překrytí z-boje platí netriviální režie výkonu, i když se může lišit v závislosti na scéně.

## <a name="next-steps"></a>Další kroky

* [Režimy vykreslování](../../concepts/rendering-modes.md)
* [Reprojekce pozdní fáze](late-stage-reprojection.md)
