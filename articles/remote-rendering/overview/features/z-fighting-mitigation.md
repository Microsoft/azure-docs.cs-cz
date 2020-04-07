---
title: Zmírnění z-boj
description: Popisuje techniky ke zmírnění z-boj artefakty
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680450"
---
# <a name="z-fighting-mitigation"></a>Zmírnění z-boj

Pokud se dva povrchy překrývají, není jasné, který z nich by měl být vykreslen nad druhým. Výsledek se dokonce mění na pixel, výsledkem artefakty závislé na zobrazení. V důsledku toho, když se kamera nebo síť pohybuje, tyto vzory blikají znatelně. Tento artefakt se nazývá *z-boj*. U aplikací AR a VR se problém zintenzivňuje, protože zařízení připojená k hlavě se přirozeně vždy pohybují. Chcete-li zabránit diváknepohodlí z-boj zmírnění funkce je k dispozici v Azure vzdálené vykreslování.

## <a name="z-fighting-mitigation-modes"></a>Z-boj zmírnění režimy

|Situaci                        | Výsledek                               |
|---------------------------------|:-------------------------------------|
|Pravidelné z-bojování               |![Z-boj](./media/zfighting-0.png)|
|Z-boj zmírnění povoleno    |![Z-boj](./media/zfighting-1.png)|
|Zvýraznění šachovní plochy povoleno|![Z-boj](./media/zfighting-2.png)|

Následující kód umožňuje zmírnění z-boj:

``` cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

> [!NOTE]
> Z-boj zmírnění je globální nastavení, které ovlivňuje všechny vykreslené sítě.

## <a name="reasons-for-z-fighting"></a>Důvody pro z-boj

Z-boj se děje hlavně ze dvou důvodů:

1. když jsou povrchy velmi daleko od kamery, přesnost jejich hodnot hloubky se snižuje a hodnoty se stávají k nerozeznání
1. když se povrchy v síti fyzicky překrývají

První problém se může vždy stát a je obtížné jej odstranit. Pokud k tomu dojde ve vaší aplikaci, ujistěte se, že poměr *blízké rovinné* vzdálenosti k *vzdálené vzdálenosti roviny* je co nejnižší jako praktický. Například blízká rovina ve vzdálenosti 0,01 a vzdálená rovina ve vzdálenosti 1000 vytvoří tento problém mnohem dříve, než mít blízkou rovinu na 0,1 a vzdálenou rovinu ve vzdálenosti 20.

Druhým problémem je indikátor špatně vytvořeného obsahu. V reálném světě, dva objekty nemohou být na stejném místě ve stejnou dobu. V závislosti na aplikaci mohou uživatelé chtít vědět, zda překrývající se povrchy existují a kde se nacházejí. Například CAD scéna budovy, která je základem pro stavbu z reálného světa, by neměla obsahovat fyzicky nemožné průsečíky povrchu. Pro vizuální kontrolu je k dispozici režim zvýraznění, který zobrazuje potenciální z-boj jako animovaný šachovní vzor.

## <a name="limitations"></a>Omezení

Za předpokladu, z-boj zmírnění je nejlepší úsilí. Neexistuje žádná záruka, že odstraní všechny z-boj. Také to bude automaticky preferují jeden povrch před druhým. Pokud tedy máte povrchy, které jsou příliš blízko u sebe, může se stát, že "špatný" povrch skončí nahoře. Běžným problémem je, když text a jiné obtisky jsou použity na povrch. S z-boj zmírnění povoleno tyto detaily by mohly snadno jen tak zmizet.

## <a name="performance-considerations"></a>Otázky výkonu

* Povolení z-boj zmírnění vynaloží málo nebo žádné režie výkonu.
* Navíc povolení z-boj překrytí vznikne netriviální výkon režie, i když se může lišit v závislosti na scéně.

## <a name="next-steps"></a>Další kroky

* [Režimy vykresl](../../concepts/rendering-modes.md)
* [Reprojekce pozdního jeviště](late-stage-reprojection.md)
