---
title: Události nástroje pro kreslení | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak přidat panel nástrojů pro kreslení na mapu pomocí Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6abe0ed88adbdf8263aa27d340fb2fff156d98e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90089339"
---
# <a name="drawing-tool-events"></a>Události nástrojů pro kreslení

Při použití nástrojů pro kreslení na mapě je vhodné reagovat na určité události, když uživatel na mapě nakreslí. Tato tabulka uvádí všechny události, které `DrawingManager` Třída podporuje.

| Událost | Popis |
|-------|-------------|
| `drawingchanged` | Je aktivována, když dojde k přidání nebo změně jakékoli souřadnice v obrazci. | 
| `drawingchanging` | Je aktivována, když se zobrazí libovolná souřadnice náhledu pro obrazec. Tato událost se například aktivuje víckrát, protože je přetažena souřadnice. | 
| `drawingcomplete` | Je aktivována, když byl tvar dokončen nebo vyčerpán z režimu úprav. |
| `drawingmodechanged` | Je aktivována při změně režimu kreslení. Nový režim kreslení se předává do obslužné rutiny události. |
| `drawingstarted` | Je aktivována, když uživatel spustí kreslení tvaru nebo vloží tvar do režimu úprav.  |

Následující kód ukazuje, jak fungují události v modulu nástrojů pro kreslení. Nakreslete obrazce na mapě a sledujte, jak se události aktivují.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Události nástrojů pro kreslení" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>události nástrojů pro kreslení</a> perem Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Příklady

Pojďme se podívat na některé běžné scénáře, které používají události nástrojů pro kreslení.

### <a name="select-points-in-polygon-area"></a>Vybrat body v oblasti mnohoúhelníku

Tento kód ukazuje, jak monitorovat událost uživatelských tvarů kresby. V tomto příkladu kód sleduje obrazce mnohoúhelníků, obdélníků a kroužků. Pak určuje, které datové body na mapě jsou v oblasti vykreslování. `drawingcomplete`Událost se používá ke spuštění logiky SELECT. Ve vybrané logice kód projde všemi datovými body na mapě. Kontroluje, zda je průsečík bodu a oblasti vykresleného obrazce. Tento příklad využívá open source knihovny [Turf.js](https://turfjs.org/) k provedení výpočtu prostorového mezioddílu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vybrat data v oblasti nakresleného mnohoúhelníku" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Vybrat data v oblasti nakresleného mnohoúhelníku</a> podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Kreslení a hledání v oblasti mnohoúhelníku

Tento kód vyhledá body zájmů v oblasti tvaru poté, co uživatel dokončil vykreslování obrazce. Kód můžete upravit a spustit kliknutím na tlačítko Upravit v psaní kódu v pravém horním rohu rámečku. `drawingcomplete`Událost se používá ke spuštění logiky hledání. Pokud uživatel nakreslí obdélník nebo mnohoúhelník, je provedena hledání v rámci geometrie. Je-li nakreslený kruh vynakreslený, používá se poloměr a Centrovaná pozice k provedení hledání bodu zájmu. `drawingmodechanged`Událost se používá k určení, kdy uživatel přepne do režimu kreslení, a tato událost vymaže plátno pro vykreslování.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kreslení a hledání v oblasti mnohoúhelníku" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se, jak pero <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>kreslí a hledejte v oblasti mnohoúhelníku</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Vytvořit nástroj pro měření

Následující kód ukazuje, jak lze pomocí události kreslení vytvořit nástroj pro měření. `drawingchanging`Slouží k monitorování obrazce při jeho vykreslování. Když uživatel přesune myš, vypočítají se rozměry obrazce. `drawingcomplete`Událost se používá k provedení konečného výpočtu obrazce po jeho vykreslení. `drawingmodechanged`Událost se používá k určení, kdy se uživatel přepne do režimu kreslení. `drawingmodechanged`Událost také vymaže plátno pro vykreslování a vymaže staré informace o měření.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Nástroj pro měření" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>Nástroj pro měření</a> pera Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Další kroky

Naučte se používat další funkce modulu nástrojů pro kreslení:

> [!div class="nextstepaction"]
> [Získání dat tvaru](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Typy interakcí a klávesové zkratky](drawing-tools-interactions-keyboard-shortcuts.md)

Další informace o modulu služby:

> [!div class="nextstepaction"]
> [Modul služeb](how-to-use-services-module.md)

Podívejte se na další ukázky kódu:

> [!div class="nextstepaction"]
> [Stránka ukázka kódu](https://aka.ms/AzureMapsSamples)
