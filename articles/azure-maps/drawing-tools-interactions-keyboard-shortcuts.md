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
ms.openlocfilehash: 267579f7b4bbfe026f3aa01b00f01f3b872cf4a6
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911661"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Typy interakcí a klávesové zkratky v modulu nástrojů pro kreslení

Tento článek popisuje různé způsoby vykreslování a úprav tvarů na mapě pomocí myši, dotykové obrazovky nebo klávesových zkratek.

Správce kreslení podporuje tři různé způsoby interakce s mapou k vykreslování tvarů.

* `click` – souřadnice se přidávají při kliknutí myší nebo dotykem.
* Souřadnice `freehand ` jsou přidány při přetahování myši nebo dotyku na mapě. 
* `hybrid` – souřadnice se přidávají při kliknutí nebo přetahování myši nebo dotykem.

## <a name="how-to-draw-shapes"></a>Kreslení tvarů

Následující text popisuje různé způsoby vykreslování tvarů na mapě. Předtím, než bude možné vykreslit libovolný tvar, musí být možnost `drawingMode` správce kreslení nastavena na podporované nastavení kreslení. To lze provést programově nebo stisknutím některého z tlačítek kreslení na panelu nástrojů. Režim kreslení zůstane povolený, i když je nakreslený tvar, což usnadňuje nakreslení dalších tvarů stejného typu. Režim kreslení může být do nečinného stavu přepnut programově nebo kliknutím na tlačítko aktuální režimy kreslení na panelu nástrojů. 

### <a name="how-to-draw-a-point"></a>Jak nakreslit bod

Je-li správce kreslení v `draw-point` režim kreslení, lze provést následující akce pro vykreslení bodů na mapě. Tyto metody pracují se všemi režimy interakce.

**Spustit kreslení**
 - Klikněte na levé tlačítko myši nebo najeďte na mapu a přidejte k mapě bod. 
 - Pokud se ukazatel myši nachází na mapě, stiskněte klávesu `F` a bod se přidá pomocí souřadnice, kde je ukazatel myši. Tím se zajistí vyšší přesnost přidávání bodu na mapu, protože na myš se stane menší pohyb z důvodu stisknutí levého tlačítka myši.
 - Pokud chcete přidat další body na mapu, klikněte na tlačítko `F`, doplňte je nebo stiskněte.
 
**Dokončit kreslení**
 - Na panelu nástrojů kreslení klikněte na libovolné tlačítko. 
 - Programově nastavíte režim kreslení. 
 - Stiskněte klávesu `C`.

**Zrušit kreslení**
 - Stiskněte klávesu `Escape`.

### <a name="how-to-draw-a-line"></a>Vykreslení čáry

Je-li Správce výkresu v režimu `draw-line`, lze provést následující akce pro vykreslení bodů na mapě v závislosti na tom, jaký je režim interakce nastaven na hodnotu.

**Spustit kreslení**
 - Režim kliknutí
   * Klikněte na levé tlačítko myši nebo najeďte na mapu a přidejte jednotlivé body čáry na mapě. Do řádku se přidá souřadnice pro každé kliknutí a dotykové ovládání. 
   * Pokud se ukazatel myši nachází na mapě, stiskněte klávesu `F` a bod se přidá pomocí souřadnice, kde je ukazatel myši. Tím se zajistí vyšší přesnost přidávání bodu na mapu, protože na myš se stane menší pohyb z důvodu stisknutí levého tlačítka myši.
   * Pokračujte v klikání, dokud se do řádku nepřidaly všechny požadované body.
 - Režim FreeHandu
   * Stiskněte levé tlačítko myši nebo klepněte na mapě na mapě a přetáhněte ukazatel myši nebo dotykové ovládání kolem. Souřadnice jsou přidány na řádek, když se pohyb myši nebo dotykového bodu pohybuje kolem mapy. Po aktivaci události myši nebo dotykového ovládání se kreslení dokončí. Četnost přidaných souřadnic je definována pomocí možností manažeři kreslení `freehandInterval`.
 - Hybridní režim
   * Alternativa mezi metodami Click a FreeHand, jak je potřeba při vykreslování jednoho řádku. Například klikněte na několik bodů, potom podržením a přetažením myši přidejte spoustu bodů a potom klikněte na několik dalších. 

**Dokončit kreslení**
 - Režim hybridního/kliknutí
   * Dvakrát klikněte na mapu v posledním bodě. 
   * Na panelu nástrojů kreslení klikněte na libovolné tlačítko. 
   * Programově nastavíte režim kreslení. 
 - Režim FreeHandu
   * Uvolněte tlačítko myši nebo dotykového bodu.
 - Stiskněte klávesu `C`.

**Zrušit kreslení**
 - Stiskněte klávesu `Escape`.

### <a name="how-to-draw-a-polygon"></a>Postup kreslení mnohoúhelníku

Je-li Správce výkresu v režimu `draw-polygon`, lze provést následující akce pro vykreslení bodů na mapě v závislosti na tom, jaký je režim interakce nastaven na hodnotu.

**Spustit kreslení**
 - Režim kliknutí
   * Klikněte na levé tlačítko myši nebo najeďte na mapu a přidejte jednotlivé body mnohoúhelníku na mapě. Do mnohoúhelníku se přidá souřadnice pro každé kliknutí a dotykové ovládání. 
   * Pokud se ukazatel myši nachází na mapě, stiskněte klávesu `F` a bod se přidá pomocí souřadnice, kde je ukazatel myši. Tím se zajistí vyšší přesnost přidávání bodu na mapu, protože na myš se stane menší pohyb z důvodu stisknutí levého tlačítka myši.
   * Až do mnohoúhelníku přidáte všechny požadované body, klikněte na pokračovat.
 - Režim FreeHandu
   * Stiskněte levé tlačítko myši nebo klepněte na mapě na mapě a přetáhněte ukazatel myši nebo dotykové ovládání kolem. Souřadnice se přidávají do mnohoúhelníku, když se pohybuje ukazatelem myši nebo dotykového bodu kolem mapy. Po aktivaci události myši nebo dotykového ovládání se kreslení dokončí. Všimněte si, že frekvence přidání souřadnic je definována vedoucími `freehandInterval` možností.
 - Hybridní režim
   * Alternativa mezi metodami Click a FreeHand, jak je potřeba při vykreslování jednoho mnohoúhelníku. Například klikněte na několik bodů, potom podržením a přetažením myši přidejte spoustu bodů a potom klikněte na několik dalších. 

**Dokončit kreslení**
 - Režim hybridního/kliknutí
   * Dvakrát klikněte na mapu v posledním bodě. 
   * Klikněte na první bod v mnohoúhelníku.
   * Na panelu nástrojů kreslení klikněte na libovolné tlačítko. 
   * Programově nastavíte režim kreslení. 
 - Režim FreeHandu
   * Uvolněte tlačítko myši nebo dotykového bodu.
 - Stiskněte klávesu `C`.

**Zrušit kreslení**
 - Stiskněte klávesu `Escape`.

### <a name="how-to-draw-a-rectangle"></a>Vykreslení obdélníku

Je-li Správce výkresu v režimu `draw-rectangle`, lze provést následující akce pro vykreslení bodů na mapě v závislosti na tom, jaký je režim interakce nastaven na hodnotu. Vygenerovaný tvar bude následovat po [Rozšířené specifikaci injson pro obdélníky](extend-geojson.md#rectangle).

**Spustit kreslení**
 - Stisknutím levého tlačítka myši nebo dotykového ovládání na mapě přidejte první roh obdélníku a přetažením vytvořte obdélník. 

**Dokončit kreslení**
 - Uvolněte tlačítko myši nebo dotykového bodu.
 - Programově nastavíte režim kreslení. 
 - Stiskněte klávesu `C`.

**Zrušit kreslení**
 - Stiskněte klávesu `Escape`.

### <a name="how-to-draw-a-circle"></a>Jak nakreslit kruh

Je-li Správce výkresu v režimu `draw-circle`, lze provést následující akce pro vykreslení bodů na mapě v závislosti na tom, jaký je režim interakce nastaven na hodnotu. Vygenerovaný tvar bude následovat po [Rozšířené specifikaci injson pro kružnice](extend-geojson.md#circle).

**Spustit kreslení**
 - Stisknutím levého tlačítka myši nebo dotykového ovládání na mapě přidejte střed kružnice a přetáhněte kruhy k poloměru. 

**Dokončit kreslení**
 - Uvolněte tlačítko myši nebo dotykového bodu.
 - Programově nastavíte režim kreslení. 
 - Stiskněte klávesu `C`.

**Zrušit kreslení**
 - Stiskněte klávesu `Escape`.

## <a name="keyboard-shortcuts"></a>Klávesové zkratky

Nástroje pro kreslení podporují klávesové zkratky, které usnadňují vykreslování a úpravu obrazců na mapě. Tyto klávesové zkratky jsou funkční, když má mapa fokus.

| Klíč      | Akce                            |
|----------|-----------------------------------|
| `C` | Dokončí všechny probíhající kresby a nastaví režim kreslení na nečinný. Fokus se přesune na prvek mapy nejvyšší úrovně.  |
| `Escape` | Zruší všechny probíhající kresby a nastaví režim kreslení na nečinný. Fokus se přesune na prvek mapy nejvyšší úrovně.  |
| `F` | Přidá souřadnici k bodu, čáře nebo mnohoúhelníku, pokud se ukazatel myši nachází na mapě. Ekvivalentní akce kliknutí na mapu v případě kliknutí nebo hybridního režimu Tato klávesová zkratka umožňuje přesnější a rychlejší nákresy, když můžete pomocí jedné ruky umístit myš a další stisknout tlačítko bez pohybu myší od gesta stisknutí klávesy CTRL. |

## <a name="next-steps"></a>Další kroky

Další informace o třídách v modulu nástrojů pro kreslení:

> [!div class="nextstepaction"]
> [Správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Panel nástrojů kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)