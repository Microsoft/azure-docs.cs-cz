---
title: Přidání panelu nástrojů výkresu do mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak přidat panel nástrojů výkresu do mapy pomocí sady Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: cf9c79f608aa3ffd1137be41ff3348f62b890867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198305"
---
# <a name="drawing-tool-events"></a>Události kreslicího nástroje

Při použití kreslicích nástrojů na mapě je užitečné reagovat na určité události, když uživatel kreslí na mapu. V této tabulce jsou uvedeny `DrawingManager` všechny události podporované třídou.

| Událost | Popis |
|-------|-------------|
| `drawingchanged` | Je aktivována, když byla přidána nebo změněna jakákoli souřadnice ve tvaru. | 
| `drawingchanging` | Je aktivována při zobrazení libovolné souřadnice náhledu pro obrazec. Například tato událost bude oheň vícekrát jako souřadnice je přetažen. | 
| `drawingcomplete` | Aktivováno po dokončení nakresleného nebo vyjmutého obrazce z režimu úprav. |
| `drawingmodechanged` | Aktivováno při změně režimu kreslení. Nový režim kreslení je předán do obslužné rutiny události. |
| `drawingstarted` | Je aktivována, když uživatel začne kreslit obrazec nebo přepne obrazec do režimu úprav.  |

Následující kód ukazuje, jak události v modulu Nástroje kreslení fungují. Kreslit tvary na mapě a sledovat, jak události oheň.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Události kreslicích nástrojů" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>události nástroje</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>kreslení perem podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Příklady

Podívejme se na některé běžné scénáře, které používají události kreslicích nástrojů.

### <a name="select-points-in-polygon-area"></a>Výběr bodů v oblasti polygonu

Tento kód ukazuje, jak sledovat událost uživatele kreslení obrazců. V tomto příkladu kód monitoruje obrazce polygonů, obdélníků a kruhů. Poté určí, které datové body na mapě jsou v rámci vylosované oblasti. Událost `drawingcomplete` se používá k aktivaci logiky výběru. Ve výběrové logice kód prochází všechny datové body na mapě. Zkontroluje, zda existuje průsečík bodu a oblasti nakresleného tvaru. Tento příklad využívá knihovnu [Turf.js](https://turfjs.org/) s otevřeným zdrojovým kódem k provedení výpočtu prostorového průsečíku.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Výběr dat v nakreslené oblasti polygonu" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na data výběru pera v<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/XWJdeja'>nakreslené oblasti polygonu</a> pomocí Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Kreslení a hledání v oblasti polygonu

Tento kód vyhledá body zájmu uvnitř oblasti obrazce po dokončení kreslení obrazce uživatelem. Kód můžete upravit a spustit kliknutím na tlačítko Upravit pero kódu v pravém horním rohu rámečku. Událost `drawingcomplete` se používá k aktivaci logiky hledání. Pokud uživatel nakreslí obdélník nebo mnohonožce, provede se hledání uvnitř geometrie. Pokud je nakreslena kružnice, poloměr a poloha středu se používají k provedení vyhledávání bodu zájmu. Událost `drawingmodechanged` se používá k určení, kdy uživatel přepne do režimu kreslení a tato událost vymaže kreslicí plátno.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kreslení a hledání v oblasti polygonu" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na kreslení perem a vyhledávání v<a href='https://codepen.io/azuremaps'>@azuremaps</a>oblasti <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>polygonu</a> pomocí Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Vytvoření měřicího nástroje

Níže uvedený kód ukazuje, jak lze události výkresu použít k vytvoření měřicího nástroje. Používá `drawingchanging` se ke sledování tvaru, jak je to kreslí. Když uživatel pohybuje myší, vypočítá se rozměry obrazce. Událost `drawingcomplete` se používá k dokončení konečného výpočtu obrazce po jeho nakresleném. Událost `drawingmodechanged` se používá k určení, kdy uživatel přepne do režimu kreslení. Událost také `drawingmodechanged` vymaže kreslicí plátno a vymaže staré informace o měření.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Měřicí nástroj" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>nástroj</a> měření<a href='https://codepen.io/azuremaps'>@azuremaps</a>pera pomocí Map Azure ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak používat další funkce modulu kreslicích nástrojů:

> [!div class="nextstepaction"]
> [Získání dat tvaru](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Typy interakcí a klávesové zkratky](drawing-tools-interactions-keyboard-shortcuts.md)

Další informace o modulu Služby:

> [!div class="nextstepaction"]
> [Modul služeb](how-to-use-services-module.md)

Podívejte se na další ukázky kódu:

> [!div class="nextstepaction"]
> [Ukázková stránka kódu](https://aka.ms/AzureMapsSamples)
