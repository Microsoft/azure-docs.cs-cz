---
title: Přidání panelu nástrojů pro kreslení na mapu | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak přidat panel nástrojů pro kreslení na mapu pomocí Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: cf9c79f608aa3ffd1137be41ff3348f62b890867
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198305"
---
# <a name="drawing-tool-events"></a>Události nástrojů pro kreslení

Při použití nástrojů pro kreslení na mapě je vhodné reagovat na určité události, když uživatel na mapě nakreslí. Tato tabulka uvádí všechny události, které třída `DrawingManager` podporuje.

| Událost | Popis |
|-------|-------------|
| `drawingchanged` | Je aktivována, když dojde k přidání nebo změně jakékoli souřadnice v obrazci. | 
| `drawingchanging` | Je aktivována, když se zobrazí libovolná souřadnice náhledu pro obrazec. Tato událost se například aktivuje víckrát, protože je přetažena souřadnice. | 
| `drawingcomplete` | Je aktivována, když byl tvar dokončen nebo vyčerpán z režimu úprav. |
| `drawingmodechanged` | Je aktivována při změně režimu kreslení. Nový režim kreslení se předává do obslužné rutiny události. |
| `drawingstarted` | Je aktivována, když uživatel spustí kreslení tvaru nebo vloží tvar do režimu úprav.  |

Následující kód ukazuje, jak fungují události v modulu nástrojů pro kreslení. Nakreslete obrazce na mapě a sledujte, jak se události aktivují.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Události nástrojů pro kreslení" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>události nástrojů pro kreslení</a> perem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Příklady

Pojďme se podívat na některé běžné scénáře, které používají události nástrojů pro kreslení.

### <a name="select-points-in-polygon-area"></a>Vybrat body v oblasti mnohoúhelníku

Tento kód ukazuje, jak monitorovat událost uživatelských tvarů kresby. V tomto příkladu kód sleduje obrazce mnohoúhelníků, obdélníků a kroužků. Pak určuje, které datové body na mapě jsou v oblasti vykreslování. Událost `drawingcomplete` slouží k aktivaci logiky SELECT. Ve vybrané logice kód projde všemi datovými body na mapě. Kontroluje, zda je průsečík bodu a oblasti vykresleného obrazce. Tento příklad využívá Open Source knihovnu [Turf. js](https://turfjs.org/) k provedení výpočtu prostorového průniku.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vybrat data v oblasti nakresleného mnohoúhelníku" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Vybrat data v oblasti nakresleného mnohoúhelníku</a> po Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Kreslení a hledání v oblasti mnohoúhelníku

Tento kód vyhledá body zájmů v oblasti tvaru poté, co uživatel dokončil vykreslování obrazce. Kód můžete upravit a spustit kliknutím na tlačítko Upravit v psaní kódu v pravém horním rohu rámečku. Událost `drawingcomplete` slouží k aktivaci logiky hledání. Pokud uživatel nakreslí obdélník nebo mnohoúhelník, je provedena hledání v rámci geometrie. Je-li nakreslený kruh vynakreslený, používá se poloměr a Centrovaná pozice k provedení hledání bodu zájmu. Událost `drawingmodechanged` slouží k určení, kdy uživatel přepne do režimu kreslení, a tato událost vymaže plátno pro vykreslování.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kreslení a hledání v oblasti mnohoúhelníku" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Kreslení a hledejte v oblasti mnohoúhelníku</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Vytvořit nástroj pro měření

Následující kód ukazuje, jak lze pomocí události kreslení vytvořit nástroj pro měření. `drawingchanging` slouží k monitorování tvaru při jeho vykreslování. Když uživatel přesune myš, vypočítají se rozměry obrazce. Událost `drawingcomplete` slouží k provedení konečného výpočtu obrazce po jeho vykreslení. Událost `drawingmodechanged` slouží k určení, kdy uživatel přepne do režimu kreslení. Také událost `drawingmodechanged` vymaže kreslicí plátno a vymaže staré informace o měření.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Nástroj pro měření" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Prohlédněte si <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>Nástroj pro měření</a> pera Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Další kroky

Naučte se používat další funkce modulu nástrojů pro kreslení:

> [!div class="nextstepaction"]
> [Získat data obrazce](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Typy interakcí a klávesové zkratky](drawing-tools-interactions-keyboard-shortcuts.md)

Další informace o modulu služby:

> [!div class="nextstepaction"]
> [Modul služeb](how-to-use-services-module.md)

Podívejte se na další ukázky kódu:

> [!div class="nextstepaction"]
> [Stránka ukázka kódu](https://aka.ms/AzureMapsSamples)
