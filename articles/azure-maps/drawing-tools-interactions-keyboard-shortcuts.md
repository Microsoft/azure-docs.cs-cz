---
title: Kreslení nástrojů typy interakce a klávesové zkratky na mapě | Mapy Microsoft Azure
description: Postup kreslení a úprav tvarů pomocí myši, dotykové obrazovky nebo klávesnice v sadě Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b609a21348222150faf5fb828f2cc38ca6658f5d
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895813"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Typy interakcí a klávesové zkratky v modulu nástrojů pro kreslení

Tento článek popisuje různé způsoby vykreslování a úprav tvarů pomocí myši, dotykové obrazovky nebo klávesových zkratek.

Správce kreslení podporuje tři různé způsoby interakce s mapou, kreslení tvarů.

* `click` -Souřadnice se přidávají při kliknutí myší nebo dotykem.
* `freehand ` -Souřadnice se přidávají při přetahování myši nebo dotyku na mapě.
* `hybrid` -Souřadnice se přidávají při kliknutí nebo přetahování myši nebo dotykem.

## <a name="how-to-draw-shapes"></a>Kreslení tvarů

 Než bude možné vykreslit libovolný tvar, nastavte `drawingMode` možnost Správce kreslení na podporované nastavení kreslení. Toto nastavení lze programovat nebo vyvolat stisknutím některého z tlačítek kreslení na panelu nástrojů. Režim kreslení zůstane povolený, i když je nakreslený tvar, což usnadňuje nakreslení dalších tvarů stejného typu. Programově nastavíte režim kreslení na stav nečinnosti. Nebo přepněte na stav nečinnosti kliknutím na tlačítko aktuální režimy kreslení na panelu nástrojů.

Další části obsahují přehled různých způsobů vykreslování tvarů na mapě.

### <a name="how-to-draw-a-point"></a>Jak nakreslit bod

Je-li správce kreslení v `draw-point` režimu kreslení, lze provést následující akce pro vykreslení bodů na mapě. Tyto metody pracují se všemi režimy interakce.

**Spustit kreslení**
 - Klikněte na levé tlačítko myši nebo najeďte na mapu, abyste přidali bod na mapu. 
 - Pokud se ukazatel myši nachází na mapě, stiskněte klávesu `F` a bod se přidá na souřadnici ukazatele myši. Tato metoda poskytuje vyšší přesnost pro přidání bodu na mapu. Z důvodu stisknutí levého tlačítka myši bude na myši menší pohyb.
 - Pokud `F` chcete přidat další body na mapu, pokračujte kliknutím na tlačítko, dotek nebo stisknutím klávesy.
 
**Dokončit kreslení**
 - Na panelu nástrojů kreslení klikněte na libovolné tlačítko. 
 - Programově nastavíte režim kreslení. 
 - Stiskněte klávesu `C` .

**Zrušit kreslení**
 - Stiskněte klávesu `Escape` .

### <a name="how-to-draw-a-line"></a>Vykreslení čáry

Je-li správce kreslení v `draw-line` režimu, lze provést následující akce pro vykreslení bodů na mapě v závislosti na režimu interakce.

**Spustit kreslení**
 - Režim kliknutí
   * Klikněte na levé tlačítko myši nebo najeďte na mapu a přidejte jednotlivé body čáry na mapě. Do řádku se přidá souřadnice pro každé kliknutí nebo dotykové ovládání. 
   * Pokud se ukazatel myši nachází na mapě, stiskněte klávesu `F` a bod se přidá na souřadnici ukazatele myši. Tato metoda poskytuje vyšší přesnost pro přidání bodu na mapu. Z důvodu stisknutí levého tlačítka myši bude na myši menší pohyb.
   * Pokračujte v klikání, dokud se do řádku nepřidaly všechny požadované body.
 - Režim FreeHandu
   * Stiskněte levé tlačítko myši nebo klepněte na mapu na mapě a přetáhněte ukazatel myši nebo dotykové ovládání kolem. Souřadnice jsou přidány na řádek, když se pohyb myši nebo dotykového bodu pohybuje kolem mapy. Po aktivaci události myši nebo dotykového ovládání se kreslení dokončí. Frekvence přidání souřadnic je definována možností manažeři kreslení `freehandInterval` .
 - Hybridní režim
   * Alternativním způsobem mezi jednotlivými metodami Click a FreeHand při sestavování jednoho řádku. Například klikněte na několik bodů, potom podržením a přetažením myši přidejte spoustu bodů a potom klikněte na několik dalších. 

**Dokončit kreslení**
 - Režim hybridního/kliknutí
   * Dvakrát klikněte na mapu v posledním bodě. 
   * Na panelu nástrojů kreslení klikněte na libovolné tlačítko. 
   * Programově nastavíte režim kreslení. 
 - Režim FreeHandu
   * Uvolněte tlačítko myši nebo dotykového bodu.
 - Stiskněte klávesu `C` .

**Zrušit kreslení**
 - Stiskněte klávesu `Escape` .

### <a name="how-to-draw-a-polygon"></a>Postup kreslení mnohoúhelníku

Je-li správce kreslení v `draw-polygon` režimu, lze provést následující akce pro vykreslení bodů na mapě v závislosti na režimu interakce.

**Spustit kreslení**
 - Režim kliknutí
   * Klikněte na levé tlačítko myši nebo najeďte na mapu, abyste přidali jednotlivé body mnohoúhelníku na mapě. Do mnohoúhelníku se přidá souřadnice pro každé kliknutí nebo dotykové ovládání. 
   * Pokud se ukazatel myši nachází na mapě, stiskněte klávesu `F` a bod se přidá na souřadnici ukazatele myši. Tato metoda poskytuje vyšší přesnost pro přidání bodu na mapu. Z důvodu stisknutí levého tlačítka myši bude na myši menší pohyb.
   * Až do mnohoúhelníku přidáte všechny požadované body, klikněte na pokračovat.
 - Režim FreeHandu
   * Stiskněte levé tlačítko myši nebo klepněte na mapu na mapě a přetáhněte ukazatel myši nebo dotykové ovládání kolem. Souřadnice se přidávají do mnohoúhelníku, když se pohybuje ukazatelem myši nebo dotykového bodu kolem mapy. Po aktivaci události myši nebo dotykového ovládání se kreslení dokončí. Frekvence přidání souřadnic je definována možností manažeři kreslení `freehandInterval` .
 - Hybridní režim
   * Alternativním způsobem mezi jednotlivými metodami Click a FreeHand při vykreslování jednoho mnohoúhelníku. Například klikněte na několik bodů, potom podržením a přetažením myši přidejte spoustu bodů a potom klikněte na několik dalších. 

**Dokončit kreslení**
 - Režim hybridního/kliknutí
   * Dvakrát klikněte na mapu v posledním bodě. 
   * Klikněte na první bod v mnohoúhelníku.
   * Na panelu nástrojů kreslení klikněte na libovolné tlačítko. 
   * Programově nastavíte režim kreslení. 
 - Režim FreeHandu
   * Uvolněte tlačítko myši nebo dotykového bodu.
 - Stiskněte klávesu `C` .

**Zrušit kreslení**
 - Stiskněte klávesu `Escape` .

### <a name="how-to-draw-a-rectangle"></a>Vykreslení obdélníku

Je-li správce kreslení v `draw-rectangle` režimu, lze provést následující akce pro vykreslení bodů na mapě v závislosti na režimu interakce. Vygenerovaný tvar bude následovat po [Rozšířené specifikaci injson pro obdélníky](extend-geojson.md#rectangle).

**Spustit kreslení**
 - Stisknutím levého tlačítka myši nebo klepnutím na mapu na mapě přidejte první roh obdélníku a přetažením vytvořte obdélník. 

**Dokončit kreslení**
 - Uvolněte tlačítko myši nebo dotykového bodu.
 - Programově nastavíte režim kreslení. 
 - Stiskněte klávesu `C` .

**Zrušit kreslení**
 - Stiskněte klávesu `Escape` .

### <a name="how-to-draw-a-circle"></a>Jak nakreslit kruh

Je-li správce kreslení v `draw-circle` režimu, lze provést následující akce pro vykreslení bodů na mapě v závislosti na režimu interakce. Vygenerovaný tvar bude následovat po [Rozšířené specifikaci injson pro kružnice](extend-geojson.md#circle).

**Spustit kreslení**
 - Stisknutím levého tlačítka myši nebo klepnutím na mapu na mapě přidejte střed kružnice a přetáhněte kruhy k poloměru. 

**Dokončit kreslení**
 - Uvolněte tlačítko myši nebo dotykového bodu.
 - Programově nastavíte režim kreslení. 
 - Stiskněte klávesu `C` .

**Zrušit kreslení**
 - Stiskněte klávesu `Escape` .

## <a name="keyboard-shortcuts"></a>Klávesové zkratky

Nástroje pro kreslení podporují klávesové zkratky. Tyto klávesové zkratky jsou funkční, když je mapa aktivní.

| Klíč      | Akce                            |
|----------|-----------------------------------|
| `C` | Dokončí všechny probíhající kresby a nastaví režim kreslení na nečinný. Fokus se přesune na prvek mapy nejvyšší úrovně.  |
| `Escape` | Zruší všechny probíhající kresby a nastaví režim kreslení na nečinný. Fokus se přesune na prvek mapy nejvyšší úrovně.  |
| `F` | Přidá souřadnici k bodu, čáře nebo mnohoúhelníku, pokud se ukazatel myši nachází na mapě. Ekvivalentní akce kliknutí na mapu v případě kliknutí nebo hybridního režimu Tato klávesová zkratka umožňuje přesnější a rychlejší nákresy. Pomocí jedné ruky můžete umístit myš a jinou klávesu pro stisknutí tlačítka bez přesunu myši na gesto stisknutí klávesy CTRL. |

## <a name="next-steps"></a>Další kroky

Další informace o třídách v modulu nástrojů pro kreslení:

> [!div class="nextstepaction"]
> [Správce výkresu](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Panel nástrojů kreslení](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)