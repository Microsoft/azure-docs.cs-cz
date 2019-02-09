---
title: Přidat vrstvu heat mapy ke službě Azure Maps | Dokumentace Microsoftu
description: Přidání vrstvu mapy heat mapy jazyka Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 957ce60b8519ccb1e3287232f7a5459a56b25bb7
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960611"
---
# <a name="add-a-heat-map-layer"></a>Přidání vrstvy heat mapy

Zahřívá mapy, označované také jako bod hustota mapy, jsou typu vizualizace dat používá k reprezentování hustotu dat s využitím celou škálu barev. Tyto se často používá k zobrazení dat na mapě "hotspotů" a jsou skvělý způsob, jak vykreslit velké bodu datových sad.  Například vykreslování desítky tisíc body v rámci zobrazení mapy jako symboly, zabírá většinu oblasti rozvržení a způsobí mnoho symbolů se vztahuje jiné, a může ztížit mnohem proniknout do data. Ale vizualizace tato datová sada stejné jako heat mapa umožňuje snadno zjistit, ve kterém jsou data bodu densest a relativní hustota do jiných oblastí. Existuje mnoho scénářů, ve které heat mapy, používají. Tady je několik příkladů;

* Data o teplotě se vykreslí jako heat mapa běžně, protože nabízí rovin útoku, pro jaké teploty mezi dvěma datovými body.
* Generování dat pro senzory šumu jako heat mapa nejen zobrazuje intenzitu zrcadlových šumu kde senzor je ale můžete také poskytnout představu o rozptylu na vzdálenosti. Úroveň šumu v jedné lokalitě nemusí být vysoká, ale pokud se překrývá se oblasti pokrytí šumu z řadu senzorů, je možné, že může docházet k vyšší úrovně šumu této překrývající se oblasti a proto bude viditelné v heat mapě.
* Vizualizace GPS trasování, která zahrnuje rychlosti jako vážená výška mapování kde intenzitu zrcadlových datových bodů podle rychlosti je skvělý způsob, jak rychle zjistit, kde byl zkrácení vozidlo.

> [!TIP]
> Vykreslí souřadnice všechny geometrie ve zdroji dat se vrstvy Heat map ve výchozím nastavení. Omezit vrstvu tak, aby je jen pro vykreslení bodu geometrie funkce nastavit `filter` vlastnost vrstva `['==', '$type', 'Point']`

## <a name="add-a-heat-map-layer"></a>Přidání vrstvy heat mapy

Pokud chcete zobrazit zdroje dat bodů v podobě heat mapa, předat do instance zdroje dat `HeatMapLayer` třídu a přidejte do mapy, jak je znázorněno zde.

<br/>

<iframe height='500' scrolling='no' title='Vrstvy jednoduché Heat mapa' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>vrstvy jednoduché Heat mapa</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

V tomto příkladu má každý bod heat vůbec úrovně přiblížení protokolu radius 10 pixelů. Při přidávání vrstvu mapy heat mapu, tato ukázka vkládá je pod popisek vrstvu. Tím se vytvoří lepší uživatelské prostředí jsou zřetelně vidět výše heat mapa popisky. Je zdrojem dat v této ukázce [USGS zemětřesení nebezpečí Program](https://earthquake.usgs.gov/) a skládá se z bodů, které představuje významné zemětřesení, ke kterým došlo v posledních 30 dní.

## <a name="customizing-the-heat-map-layer"></a>Přizpůsobení vrstvu heat mapy

Předchozí příklad nastavení možností pomocí protokolu radius a krytí upravit heat mapa. Poskytuje několik možností pro přizpůsobení; vrstvu heat mapy

* `radius`: Definuje pixel radius, ve kterém k vykreslení datových bodů. Poloměr je možné nastavit jako číslo s pevným nebo jako výraz. Vlastnosti autorefresh pomocí výrazu, je možné škálovat radius založené na úroveň zvětšení, které se zobrazí představují konzistentní prostorových oblasti na mapě (například 5 mil radius).
* `color`: Určuje, jak barevně zvýrazněné heat mapa. Palety barev přechodu se často používá pro heat mapy, ale stupňovitým barev, které jsou také užitečné, pokud mají být váš heat mapa vypadat více jako tvarování dat palety. Tyto barvy palety definovat barvy od minima k hodnotě maximální hustoty. Hodnoty barev pro heat mapy jsou určeny jako výraz na `heatmap-density` hodnotu. Barvy v indexu 0 ve výrazu přechodu nebo výchozí barvu barvu krok definuje barvu, oblasti, kde není žádná data a je možné definovat barvu pozadí. Mnoho chtít nastavit tuto hodnotu a je transparentní nebo poloprůhledného black. Tady jsou příklady výrazů barva;

| Barva barevného přechodu – výraz | Stupňovité barva – výraz | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpolovat.<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["lineární"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "fialové",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, '#fb00fb.<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff.<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"krok",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"transparentní.<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "navy",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "navy",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "zelená",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "", žlutý<br/>&nbsp;&nbsp;&nbsp;&nbsp;1,00 "red"<br/>\] |   

* `opacity`: Určuje, jak neprůhledné nebo průhledné heat mapa vrstva je.
* `intensity`: Násobitel se vztahuje na váhu jednotlivých bodů řady ke zvýšení celkové intenzitu zrcadlových Heat mapě. To pomáhá provádět malé rozdíly v hmotnosti datových bodů snadnější vizualizace.
* `weight`: Ve výchozím nastavení všechny datové body mají lepší zvolit váhu 1, takže všechny datové body jsou vyváženy rovnoměrně. Možnost Váha funguje jako násobitel a je možné nastavit jako číslo nebo výraz. Pokud číslo je nastavená na váhu, Dejme tomu, že 2, bylo by ekvivalentní umístění datových bodů na mapě dvakrát, tím zdvojnásobení hustotu. Nastavení možnosti váhou na číslo vykreslí heat mapa podobným způsobem intenzita možnost použití. Ale pokud použijete výraz váhu jednotlivých bodů řady může být založen na některé metriky ve vlastnostech jednotlivých bodů řady. Využijte zemětřesení data jako příklad datových bodů představují zemětřesení. Důležitou metrikou datových bodů zemětřesení má, je hodnota velikosti. Zemětřesení stát celou dobu, ale většina mají nízké velikost a ještě nejsou popisovač. Pomocí hodnoty velikosti ve výrazu každému datovému bodu přiřadit váhu vám umožní významnější zemětřesení, aby se dala lépe vyjádřit v rámci heat mapa.
* Kromě možností uvedených základní vrstvě; minimální/maximální přiblížení, viditelné a filtrovat, je zde také `source` možnost, pokud chcete aktualizovat zdroj dat a `source-layer` Pokud zdroj dat je zdroj dlaždice vektoru.

Zde je nástroj, k otestování možností vrstvy různých heat mapy.

<br/>

<iframe height='700' scrolling='no' title='Heat mapa vrstvy možnosti' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>zahřívá možností vrstvy mapy</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Když povolíte clustering pro zdroj dat, jsou jako Clusterované bod seskupeny body, které jsou blízko sebe. Počet bodů, které každý cluster můžete použít jako výraz tloušťka pro heat mapa a výrazně snížit počet bodů, které mají být vykreslit. Počet bodů clusteru je uložené v `point_count property` bodu funkce, jak je znázorněno níže. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Pokud existuje pouze několik málo pixelů clusteringu radius se být malou visual rozdíl vykreslování. Větší radius se seskupit více bodů do každý cluster a zlepšit výkon Heat mapě, ale mají snadněji postřehnutelné bude rozdíly.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidat vrstvu symbol](./map-add-pin.md)

