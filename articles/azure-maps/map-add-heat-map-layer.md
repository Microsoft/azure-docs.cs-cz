---
title: Přidat vrstvu Heat mapy do mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak přidat vrstvu Heat mapy k mapě pomocí webové sady SDK Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ead30a80a6568e72f922f355916d31121b49a93b
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911207"
---
# <a name="add-a-heat-map-layer"></a>Přidání vrstvy heat mapy

Heat mapy, označované také jako mapy hustoty bodů, jsou typem vizualizace dat, která představuje hustotu dat pomocí rozsahu barev. Často se používají k zobrazení dat "aktivních bodů" na mapě a představují skvělý způsob, jak vykreslovat datové sady s velkými čárkami. 

Například vykreslování desítků tisíc bodů v rámci zobrazení mapy jako symbolů pokrývá většinu oblastí rozvržení. To nejspíš vede k tomu, že mnoho symbolů se vzájemně překrývá, takže je obtížné získat z nich mnohem lepší přehled o datech. Vizualizace stejné datové sady jako Heat mapa však usnadňuje zjištění, kde jsou data bodu nejhustá, a relativní hustota jiným oblastem.

Můžete použít Heat mapy v mnoha různých scénářích, mezi které patří:

- **Data o teplotě**: poskytuje přibližné hodnoty pro to, co je teplota mezi dvěma datovými body.
- **Data pro senzory hluku**: ukazuje nejen intenzitu hluku, kde je senzor, ale může také poskytnout přehled o rozptylu v průběhu určité vzdálenosti. Úroveň hluku v některém webu nemusí být vysoká. Pokud je však oblast pokrytí hluku od více senzorů překryta, je možné, že tato překrývající se oblasti může vyskytnout vyšší úrovně šumu, a proto by se v Heat mapě zobrazila.
- **GPS Trace**: zahrnuje rychlost jako mapu s váženou výškou, kde je intenzita jednotlivých datových bodů založena na rychlosti. Například poskytuje způsob, jak zjistit, kde bylo vozidlo urychleno.

> [!TIP]
> Vrstvy Heat mapy ve výchozím nastavení vykreslují souřadnice všech geometrií ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykreslí pouze funkce geometrie bodu, nastavte vlastnost `filter` vrstvy na `['==', ['geometry-type'], 'Point']`. Pokud chcete zahrnout i funkce systému MultiPoint, nastavte vlastnost `filter` vrstvy na `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Přidání vrstvy heat mapy

Chcete-li vykreslit zdroj dat bodů jako Heat mapu, předejte zdroj dat do instance třídy `HeatMapLayer` a přidejte jej do mapy.

V následujícím kódu má každý tepelný bod poloměr 10 pixelů na všech úrovních přiblížení. Když přidáte vrstvu Heat mapy na mapu, Tato ukázka ji vloží pod vrstvu popisku a vytvoří tak lepší uživatelské prostředí. Popisky jsou jasně viditelné nad Heat mapou. Data v této ukázce se nastavují v [programu sadě USGS zemětřesení s riziky](https://earthquake.usgs.gov/)a představují významné zemětřesení, ke kterým došlo během posledních 30 dnů.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Zde je ukázka kompletního běžícího kódu předchozího kódu.

<br/>

<iframe height='500' scrolling='no' title='Jednoduchá vrstva Heat mapy' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>vrstvu jednoduchá Heat mapa</a> pera Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Přizpůsobení vrstvy Heat mapy

Předchozí příklad upravil Heat mapu nastavením možnosti poloměr a krytí. Vrstva Heat mapy nabízí několik možností přizpůsobení, včetně:

* `radius`: definuje poloměr pixelu, ve kterém se má vykreslovat každý datový bod. Poloměr můžete nastavit jako pevné číslo nebo jako výraz. Pomocí výrazu můžete škálovat poloměr na základě úrovně přiblížení a znázornit konzistentní prostorové oblasti na mapě (například poloměr poloměru 5 km).
* `color`: Určuje způsob barevného nabarvení Heat mapy. Barevný přechod je běžnou funkcí Heat mapy a můžete dosáhnout tohoto efektu pomocí `interpolate`ho výrazu. Můžete také použít výraz `step` pro Colorizing Heat mapy a rozvrhnout hustotu vizuálně do rozsahů, které se podobají obrysu nebo rozvržení paprskového stylu. Tyto palety barev definují barvy z minima na maximální hodnotu hustoty. 

  Hodnoty barvy pro Heat mapy se určují jako výraz na hodnotu `heatmap-density`. Barva v indexu 0 ve výrazu interpolace nebo výchozí barva výrazu kroku definuje barvu oblasti, kde nejsou žádná data. Tuto možnost můžete použít k definování barvy pozadí. Tato hodnota je často nastavená na transparentní nebo na černou, která je částečně průhledná. 
   
  Tady jsou příklady barevných výrazů:

  | Výraz barvy interpolace | Výraz se zvýrazněnou barvou | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' interpolace ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[lineární\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[heatmapu-hustota\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transparentní",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, fialová,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, ' #fb00fb ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, ' #00c3ff '<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Step",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[heatmapu-hustota\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Transparent",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, ' námořnická modrá ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "zelená",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "žlutá",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, Red<br/>\] |   

- `opacity`: Určuje způsob, jakým je vrstva Heat mapy neprůhledná nebo průhledná.
- `intensity`: aplikuje násobitel na váhu jednotlivých datových bodů pro zvýšení celkové intenzity heatmapu. To pomáhá zajistit, aby byly malé rozdíly v váhy datových bodů snazší vizualizace.
- `weight`: ve výchozím nastavení mají všechny datové body váhu 1 a mají stejnou váhu. Možnost váhy funguje jako násobitel a můžete ji nastavit jako číslo nebo výraz. Pokud je číslo (například 2) nastaveno jako váha, je ekvivalentem umístění každého datového bodu na mapě dvakrát, což zdvojnásobuje hustotu. Nastavení možnosti váhy na číslo vykreslí Heat mapu podobným způsobem, jak použít možnost intenzita. 

  Pokud však použijete výraz, může být váha každého datového bodu založena na vlastnostech jednotlivých datových bodů. Předpokládejme například, že každý datový bod představuje objekt zemětřesení. Důležitou metrikou je, že každý datový bod zemětřesení má hodnotu velikosti. K zemětřesení dochází po celou dobu, ale většina má nízkou velikost a ještě neexistují. Pomocí hodnoty velikost ve výrazu pro přiřazení váhy každému datovému bodu získáte lepší reprezentaci významnosti zemětřesení v rámci Heat mapy.
- `source` a `source-layer`: umožňuje aktualizovat zdroj dat.

Zde je nástroj pro otestování různých možností vrstvy Heat mapy.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy tepelné mapy' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'></a>se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Možnosti vrstvy mapy tepelného</a> pera.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konzistentní Heat mapa s přiblížením

Ve výchozím nastavení mají poloměry datových bodů vykreslených v vrstvě Heat mapy u všech úrovní přiblížení pevný obraz v pixelech. Při přiblížení mapy se data agreguje společně a vrstva Heat mapy vypadá jinak. 

Použijte výraz `zoom` pro horizontální navýšení kapacity poloměru pro každou úroveň přiblížení, takže každý datový bod pokrývá stejnou fyzickou oblast mapy. Tím se vrstva Heat mapy bude podobat statickým a konzistentním způsobem. Každá úroveň přiblížení mapy má dvakrát a vodorovně tolik pixelů jako předchozí úroveň přiblížení. 

Škálování poloměru tak, aby dvojnásobek každé úrovně přiblížení vytvoří Heat mapu, která vypadá konzistentně na všech úrovních přiblížení. Chcete-li to provést, použijte `zoom` se základními výrazy `exponential interpolation`, jak je znázorněno v následující ukázce. Přiblížením mapy zjistíte, jak se Heat mapa škáluje s úrovní přiblížení.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konzistentní Heat mapa s přiblížením" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na v <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>konzistentním Heat Azure Maps mapě s přiblížením</a> na pero.
</iframe>

> [!TIP]
> Pokud povolíte clusteringu na zdroji dat, body, které jsou blízko sebe, jsou seskupeny dohromady jako clusterovaný bod. Počet bodů každého clusteru můžete použít jako výraz váhy pro Heat mapu a významně snížit počet bodů, které je nutné vykreslit. Počet bodů clusteru je uložený ve vlastnosti `point_count` funkce Point: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Pokud je poloměr clusteringu jenom v několika pixelech, v vykreslování se vyskytuje malý vizuální rozdíl. Větší skupiny RADIUS mají více bodů do každého clusteru a zvyšují výkon heatmapu.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Další příklady kódu pro přidání do map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)
