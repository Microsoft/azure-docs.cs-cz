---
title: Přidat vrstvu Heat mapy do Azure Maps | Microsoft Docs
description: Postup přidání vrstvy Heat mapy do Azure Maps webové sady SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f7115e7c8b95efd0e3bbc8a788528878c2d1f092
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484299"
---
# <a name="add-a-heat-map-layer"></a>Přidání vrstvy heat mapy

Heat mapy, označované také jako mapy hustoty bodů, jsou typem vizualizace dat, která představuje hustotu dat pomocí rozsahu barev. Často se používají k zobrazení dat "aktivních bodů" na mapě a představují skvělý způsob, jak vykreslovat sady velkých bodů dat.  Například vykreslování desítkových tisíc bodů v rámci zobrazení mapy jako symbolů, pokrývá většinu oblastí rozvržení a má za následek, že mnoho symbolů se vzájemně překrývá, takže je obtížné získat z nich mnohem lepší přehled o datech. Vizualizace stejné sady dat jako Heat mapa ale usnadňuje zjištění, kde jsou data bodu nejhustá a relativní hustota jiným oblastem. Existuje mnoho scénářů, ve kterých se používají Heat mapy. Tady je několik příkladů;

- Data o teplotě se běžně vykreslují jako Heat mapa, protože poskytuje aproximace pro to, co teplota mezi dvěma datovými body.
- Vykreslování dat pro senzory hluku jako Heat mapa nejen zobrazuje intenzitu hluku, kde je senzor, ale může také poskytnout přehledy o rozptylu v průběhu určité vzdálenosti. Úroveň hluku v jedné lokalitě nemusí být vysoká, ale pokud se oblast pokrytí hluku od více senzorů překrývá, je možné, že tato překrývající se oblasti může vyskytnout vyšší úrovně šumu, a proto by se měla zobrazit v Heat mapě.
- Vizualizace sledovacího trasování, které zahrnuje rychlost jako mapu s váženou výškou, kde je intenzita jednotlivých datových bodů založena na rychlosti, je skvělým způsobem, jak zjistit, kde bylo vozidlo urychleno.

> [!TIP]
> Vrstvy tepelné mapy ve výchozím nastavení vykreslí souřadnice všech geometrií ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykresluje pouze funkce geometrie bodů, nastavte vlastnost `filter` vrstvy na `['==', ['geometry-type'], 'Point']` nebo `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`, pokud chcete zahrnout i funkce systému MultiPoint.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Přidání vrstvy heat mapy

Chcete-li vykreslit zdroj dat bodů jako Heat mapu, předejte svůj zdroj dat do instance třídy `HeatMapLayer` a přidejte jej na mapu, jak je znázorněno zde.

V následujícím kódu má každý tepelný bod poloměr 10 pixelů na všech úrovních přiblížení. Při přidávání vrstvy Heat mapy na mapu je tato ukázka vložena pod vrstvu popisku, aby se vytvořilo lepší uživatelské prostředí, protože popisky jsou jasně viditelné nad Heat mapou. Data v této ukázce se nastavují v [programu sadě USGS zemětřesení nebezpečí](https://earthquake.usgs.gov/) a představují významné zemětřesení, ke kterým došlo během posledních 30 dnů.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a data set of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Jednoduchá vrstva Heat mapy' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>vrstvu jednoduchá Heat mapa</a> pera Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-the-heat-map-layer"></a>Přizpůsobení vrstvy Heat mapy

Předchozí příklad upravil Heat mapu nastavením možnosti poloměr a krytí. Vrstva Heat mapy nabízí několik možností přizpůsobení;

* `radius`: definuje poloměr pixelu, ve kterém se má vykreslovat každý datový bod. Poloměr se dá nastavit jako pevné číslo nebo jako výraz. Pomocí výrazu je možné škálovat poloměr na základě úrovně přiblížení, která představuje konzistentní prostorovou oblast na mapě (například poloměr 5-km).
* `color`: Určuje způsob barevného nabarvení Heat mapy. Barevný přechod se často používá pro Heat mapy a dá se dosáhnout pomocí `interpolate`ho výrazu. Použití výrazu `step` pro Colorizing Heat mapa rozdělí hustotu vizuálně do rozsahů, které se tak podobají obrysovým rozvržením obrysu nebo paprskového stylu. Tyto palety barev definují barvy z minima na maximální hodnotu hustoty. Hodnoty barvy pro Heat mapy jsou zadány jako výraz na hodnotě `heatmap-density`. Barva v indexu 0 ve výrazu interpolace nebo výchozí barva výrazu kroku definuje barvu oblasti, kde nejsou žádná data, a lze ji použít k definování barvy pozadí. Mnoho preferuje nastavení této hodnoty na transparentní nebo částečně průhlednou černou. Tady jsou příklady barevných výrazů;

| Výraz barvy interpolace | Výraz se zvýrazněnou barvou | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' interpolace ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[lineární\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[heatmapu-hustota\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transparentní",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, fialová,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, ' #fb00fb ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, ' #00c3ff '<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Step",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[heatmapu-hustota\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Transparent",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, ' námořnická modrá ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "zelená",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "žlutá",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, Red<br/>\] | 

- `opacity`: Určuje způsob, jakým je vrstva Heat mapy neprůhledná nebo průhledná.
- `intensity`: aplikuje násobitel na váhu jednotlivých datových bodů, aby se zvýšila celková intenzita heatmapu a usnadnila se jejich použití v malých rozdílech v hmotnosti datových bodů.
- `weight`: ve výchozím nastavení mají všechny datové body váhu 1, takže všechny datové body jsou rovnoměrně vážené. Možnost váhy funguje jako násobitel a lze ji nastavit jako číslo nebo výraz. Pokud je číslo nastavené jako váha, řekněme 2, že by to byl ekvivalent umístění každého datového bodu na mapě dvakrát, takže hustota se zdvojnásobí. Nastavení možnosti váhy na číslo vykreslí Heat mapu podobným způsobem, jak použít možnost intenzita. Pokud je však použit výraz, váha každého datového bodu může být založena na vlastnostech jednotlivých datových bodů. Zemětřesení data jako příklad, každý datový bod představuje zemětřesení. Důležitou metrikou každého datového bodu zemětřesení je hodnota. K zemětřesení dochází po celou dobu, ale většina má nízkou velikost, ani to nevedou. Použití hodnoty velikost ve výrazu pro přiřazení váhy jednotlivým datovým bodům umožní větší zemětřeseníi lepší reprezentace v rámci Heat mapy.
- Kromě možností základní vrstvy; min/max přiblížení, Visible a Filter, existuje také možnost `source`, pokud chcete aktualizovat zdroj dat a možnost `source-layer`, pokud je zdroj dat zdrojem vektorové dlaždice.

Tady je nástroj pro otestování různých možností vrstvy Heat mapy.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy tepelné mapy' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'></a>se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Možnosti vrstvy mapy tepelného</a> pera.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konzistentní Heat mapa s přiblížením

Ve výchozím nastavení mají poloměry datových bodů vykreslených v vrstvě Heat mapy u všech úrovní přiblížení pevný obraz v pixelech. Jak je mapa zvětšena, agregace dat a vrstva Heat mapy vypadá jinak. Výraz `zoom` lze použít k horizontálnímu škálování poloměru pro každou úroveň přiblížení tak, že každý datový bod pokrývá stejnou fyzickou oblast mapy. Díky tomu bude vrstva Heat mapy vypadat spolehlivě a konzistentní. Každá úroveň přiblížení mapy má dvakrát a vodorovně tolik pixelů jako předchozí úroveň přiblížení. Změna velikosti poloměru tak, aby se u každé úrovně přiblížení zdvojnásoba, vytvořila Heat mapu, která bude vypadat konzistentně na všech úrovních přiblížení. To lze provést pomocí `zoom` se výrazem základní 2 `exponential interpolation`, jak je znázorněno v následující ukázce. Přiblížením mapy zjistíte, jak se Heat mapa škáluje s úrovní přiblížení.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konzistentní Heat mapa s přiblížením" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na v <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>konzistentním Heat Azure Maps mapě s přiblížením</a> na pero.
</iframe>

> [!TIP]
> Povolením clusteringu u zdroje dat se vzájemně seskupují body, které jsou blízko sebe seskupené jako clusterový bod. Počet bodů každého clusteru lze použít jako výraz váhy pro Heat mapu a významně snížit počet bodů, které je nutné vykreslit. Počet bodů clusteru je uložený ve vlastnosti `point_count` funkce Point, jak je znázorněno níže. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Pokud je poloměr clusteringu jenom v několika pixelech, bude vykreslování malým rozdílem. Větší poloměr bude seskupovat více bodů do každého clusteru a zvýší výkon heatmapu, ale podrobnější rozdíly budou.

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