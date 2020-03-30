---
title: Typy interakcí nástrojů pro kreslení a klávesové zkratky na mapě | Mapy Microsoft Azure
description: Jak kreslit a upravovat obrazce pomocí myši, dotykové obrazovky nebo klávesnice ve webové sadě Microsoft Azure Maps SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fb8a1e1a8c29086553500bdad2e4604d1e1ef471
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198288"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Typy interakcí a klávesové zkratky v modulu kreslicí nástroje

Tento článek popisuje všechny různé způsoby kreslení a úprav y pomocí myši, dotykové obrazovky nebo klávesových zkratek.

Správce výkresů podporuje tři různé způsoby interakce s mapou, kreslení tvarů.

* `click`- Souřadnice jsou přidány, když kliknete myší nebo dotykem.
* `freehand `- Souřadnice jsou přidány, když je myš nebo dotek přetažen na mapě.
* `hybrid`- Souřadnice jsou přidány, když myši nebo dotyku je klikli nebo přetáhli.

## <a name="how-to-draw-shapes"></a>Jak kreslit tvary

 Před nakreslením libovolného `drawingMode` obrazce nastavte volbu správce výkresu na podporované nastavení výkresu. Toto nastavení lze naprogramovat nebo vyvolat stisknutím jednoho z tlačítek kreslení na panelu nástrojů. Režim kreslení zůstává povolen i po nakreslení tvaru, což usnadňuje kreslení dalších tvarů stejného typu. Programově nastavte režim kreslení do stavu nečinnosti. Nebo přepněte do stavu nečinnosti klepnutím na tlačítko aktuálního režimu kreslení na panelu nástrojů.

V následujících částech jsou popsány všechny různé způsoby, jak lze na mapě kreslit tvary.

### <a name="how-to-draw-a-point"></a>Jak nakreslit bod

Když je správce `draw-point` výkresu v režimu kreslení, lze provést následující akce pro kreslení bodů na mapě. Tyto metody pracují se všemi režimy interakce.

**Zahájení kreslení**
 - Kliknutím na levé tlačítko myši nebo se dotkněte mapy a přidejte do mapy bod. 
 - Pokud je myš nad mapou, stiskněte klávesu `F` a bod bude přidán na souřadnici ukazatele myši. Tato metoda poskytuje vyšší přesnost pro přidání bodu do mapy. Tam bude menší pohyb na myši v důsledku stisknutí pohybu levého tlačítka myši.
 - Kliknutím, dotykem nebo `F` stisknutím tlačítka přidáte do mapy další body.
 
**Dokončit kreslení**
 - Klikněte na libovolné tlačítko v pruhu nástrojů výkresu. 
 - Programově nastavte režim kreslení. 
 - Stiskněte `C` klávesu.

**Zrušit výkres**
 - Stiskněte `Escape` klávesu.

### <a name="how-to-draw-a-line"></a>Jak nakreslit čáru

Když je správce `draw-line` výkresu v režimu, lze v závislosti na režimu interakce provést následující akce pro kreslení bodů na mapě.

**Zahájení kreslení**
 - Režim kliknutí
   * Klikněte na levé tlačítko myši nebo se dotkněte mapy a přidejte každý bod čáry na mapě. Pro každé kliknutí nebo dotyk je k čáře přidána souřadnice. 
   * Pokud je myš nad mapou, stiskněte klávesu `F` a bod bude přidán na souřadnici ukazatele myši. Tato metoda poskytuje vyšší přesnost pro přidání bodu do mapy. Tam bude menší pohyb na myši v důsledku stisknutí pohybu levého tlačítka myši.
   * Pokračujte kliknutím, dokud nebudou do řádku přidány všechny požadované body.
 - Režim od ruky
   * Stiskněte levé tlačítko myši nebo dotkněte se mapy a přetáhněte myš nebo dotykový bod kolem. Souřadnice jsou přidány k čáře jako myš nebo dotykový bod se pohybuje po mapě. Jakmile je spuštěna událost myši nebo retušování, je výkres dokončen. Frekvence přidávání souřadnic je definována `freehandInterval` možností správce výkresů.
 - Hybridní režim
   * Při kreslení jedné čáry můžete podle potřeby střídavě a od ruky. Například klikněte na několik bodů, pak podržte a přetáhněte myší přidat spoustu bodů, pak klepněte na několik dalších. 

**Dokončit kreslení**
 - Hybridní/Click režim
   * Poklepejte na mapu v posledním bodě. 
   * Klikněte na libovolné tlačítko v pruhu nástrojů výkresu. 
   * Programově nastavte režim kreslení. 
 - Režim od ruky
   * Uvolněte tlačítko myši nebo dotykový bod.
 - Stiskněte `C` klávesu.

**Zrušit výkres**
 - Stiskněte `Escape` klávesu.

### <a name="how-to-draw-a-polygon"></a>Jak nakreslit polygon

Když je správce `draw-polygon` výkresu v režimu, lze v závislosti na režimu interakce provést následující akce pro kreslení bodů na mapě.

**Zahájení kreslení**
 - Režim kliknutí
   * Klikněte na levé tlačítko myši nebo se dotkněte mapy a přidejte každý bod polygonu na mapě. Pro každé kliknutí nebo dotyk se do polyga se přidá souřadnice. 
   * Pokud je myš nad mapou, stiskněte klávesu `F` a bod bude přidán na souřadnici ukazatele myši. Tato metoda poskytuje vyšší přesnost pro přidání bodu do mapy. Tam bude menší pohyb na myši v důsledku stisknutí pohybu levého tlačítka myši.
   * Pokračujte kliknutím, dokud nebudou všechny požadované body přidány do polygonu.
 - Režim od ruky
   * Stiskněte levé tlačítko myši nebo dotkněte se mapy a přetáhněte myš nebo dotykový bod kolem. Souřadnice jsou přidány do polygu, když se myš nebo dotykový bod pohybuje po mapě. Jakmile je spuštěna událost myši nebo retušování, je výkres dokončen. Frekvence přidávání souřadnic je definována `freehandInterval` možností správce výkresů.
 - Hybridní režim
   * Při kreslení jednoho polygonu se podle potřeby stříhá mezi metodami kliknutí a od ruky. Například klikněte na několik bodů, pak podržte a přetáhněte myší přidat spoustu bodů, pak klepněte na několik dalších. 

**Dokončit kreslení**
 - Hybridní/Click režim
   * Poklepejte na mapu v posledním bodě. 
   * Klikněte na první bod v polygu.
   * Klikněte na libovolné tlačítko v pruhu nástrojů výkresu. 
   * Programově nastavte režim kreslení. 
 - Režim od ruky
   * Uvolněte tlačítko myši nebo dotykový bod.
 - Stiskněte `C` klávesu.

**Zrušit výkres**
 - Stiskněte `Escape` klávesu.

### <a name="how-to-draw-a-rectangle"></a>Jak nakreslit obdélník

Když je správce `draw-rectangle` výkresu v režimu, lze v závislosti na režimu interakce provést následující akce pro kreslení bodů na mapě. Generovaný tvar bude následovat [rozšířené geojson specifikace pro obdélníky](extend-geojson.md#rectangle).

**Zahájení kreslení**
 - Stisknutím levého tlačítka myši nebo stisknutím tlačítka na mapě přidejte první roh obdélníku a tažením obdélník vytvořte. 

**Dokončit kreslení**
 - Uvolněte tlačítko myši nebo dotykový bod.
 - Programově nastavte režim kreslení. 
 - Stiskněte `C` klávesu.

**Zrušit výkres**
 - Stiskněte `Escape` klávesu.

### <a name="how-to-draw-a-circle"></a>Jak nakreslit kruh

Když je správce `draw-circle` výkresu v režimu, lze v závislosti na režimu interakce provést následující akce pro kreslení bodů na mapě. Generovaný tvar bude následovat [podle rozšířené specifikace GeoJSON pro kružnice](extend-geojson.md#circle).

**Zahájení kreslení**
 - Stisknutím levého tlačítka myši nebo dotykem na mapě přidejte střed kruhu a tažením dodáte kruhům poloměr. 

**Dokončit kreslení**
 - Uvolněte tlačítko myši nebo dotykový bod.
 - Programově nastavte režim kreslení. 
 - Stiskněte `C` klávesu.

**Zrušit výkres**
 - Stiskněte `Escape` klávesu.

## <a name="keyboard-shortcuts"></a>Klávesové zkratky

Nástroje pro kreslení podporují klávesové zkratky. Tyto klávesové zkratky jsou funkční, když je mapa v centru pozornosti.

| Klíč      | Akce                            |
|----------|-----------------------------------|
| `C` | Dokončí všechny probíhající výkresy a nastaví režim kreslení na nečinný. Fokus se přesune na prvek mapy nejvyšší úrovně.  |
| `Escape` | Zruší všechny probíhající výkresy a nastaví režim výkresu na nečinný. Fokus se přesune na prvek mapy nejvyšší úrovně.  |
| `F` | Přidá souřadnici k bodu, úsece nebo polygu, pokud je myš nad mapou. Ekvivalentní akce kliknutí na mapu v režimu kliknutí nebo hybridního režimu. Tato zkratka umožňuje přesnější a rychlejší výkresy. Jednou rukou můžete umístit myš a druhou stisknout tlačítko bez pohybu myši z gesta stisknutí. |

## <a name="next-steps"></a>Další kroky

Další informace o třídách v modulu kreslicích nástrojů:

> [!div class="nextstepaction"]
> [Správce výkresů](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Panel nástrojů Kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
