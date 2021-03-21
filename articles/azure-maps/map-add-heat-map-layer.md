---
title: Přidat vrstvu Heat mapy do mapy | Mapy Microsoft Azure
description: Přečtěte si, jak vytvořit Heat mapu. Informace o tom, jak pomocí Azure Maps Web SDK přidat vrstvu Heat mapy do mapy. Přečtěte si, jak přizpůsobit vrstvy Heat mapy.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b15ee7091a68f7fcc79c71877c4af28b511b84de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680148"
---
# <a name="add-a-heat-map-layer"></a>Přidání vrstvy heat mapy

Heat mapy, označované také jako mapy hustoty bodů, jsou typem vizualizace dat. Slouží k reprezentaci hustoty dat pomocí rozsahu barev a zobrazení dat "aktivních bodů" na mapě. Heat mapy představují skvělý způsob vykreslování datových sad s velkým počtem bodů. 

Vykreslení desítky tisíc bodů, protože symboly mohou pokrývat většinu oblastí rozvržení. Tento případ nejspíš způsobí, že mnoho symbolů překrývá. Je obtížné získat lepší informace o datech. Vizualizace stejné datové sady jako Heat mapy však usnadňuje zobrazení hustoty a relativní hustoty jednotlivých datových bodů.

Můžete použít Heat mapy v mnoha různých scénářích, mezi které patří:

- **Data o teplotě**: poskytuje přibližné hodnoty pro to, co je teplota mezi dvěma datovými body.
- **Data pro senzory hluku**: ukazuje nejen intenzitu hluku, kde je senzor, ale může také poskytnout přehled o rozptylu v rámci určité vzdálenosti. Úroveň hluku v některém webu nemusí být vysoká. Pokud se oblast pokrytí hluku od více senzorů překrývá, je možné, že se tato překrývající oblast dostanou o vyšší úrovně hluku. V takovém případě by se překrytá oblast zobrazila v Heat mapě.
- **GPS Trace**: zahrnuje rychlost jako mapu s váženou výškou, kde je intenzita jednotlivých datových bodů založena na rychlosti. Tato funkce například poskytuje způsob, jak zjistit, kde bylo vozidlo urychleno.

> [!TIP]
> Vrstvy Heat mapy ve výchozím nastavení vykreslují souřadnice všech geometrií ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykreslí pouze funkce geometrie bodu, nastavte `filter` vlastnost vrstvy na `['==', ['geometry-type'], 'Point']` . Pokud chcete zahrnout i funkce systému MultiPoint, nastavte `filter` vlastnost vrstvy na `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` .

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="add-a-heat-map-layer"></a>Přidání vrstvy heat mapy

Chcete-li vykreslit zdroj dat bodů jako Heat mapu, předejte zdroj dat do instance `HeatMapLayer` třídy a přidejte jej do mapy.

V následujícím kódu má každý tepelný bod poloměr 10 pixelů na všech úrovních přiblížení. Aby bylo zajištěno lepší uživatelské prostředí, je Heat mapa pod vrstvou popisku. Popisky zůstávají jasně viditelné. Data v této ukázce se nacházejí v [programu sadě USGS zemětřesení nebezpečí](https://earthquake.usgs.gov/). Je pro významné zemětřesení, ke kterým došlo během posledních 30 dnů.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Zde je ukázka kompletního běžícího kódu předchozího kódu.

<br/>

<iframe height='500' scrolling='no' title='Jednoduchá vrstva Heat mapy' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>vrstvu jednoduchá Heat mapa</a> pera podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Přizpůsobení vrstvy Heat mapy

Předchozí příklad upravil Heat mapu nastavením možnosti poloměr a krytí. Vrstva Heat mapy nabízí několik možností přizpůsobení, včetně:

* `radius`: Definuje poloměr pixelu, ve kterém se má vykreslovat každý datový bod. Poloměr můžete nastavit jako pevné číslo nebo jako výraz. Pomocí výrazu můžete škálovat poloměr na základě úrovně přiblížení a znázornit konzistentní prostorové oblasti na mapě (například poloměr poloměru 5 km).
* `color`: Určuje, jak je Heat mapa barevně barevná. Barevný přechod je běžnou funkcí Heat mapy. Efekt můžete dosáhnout pomocí `interpolate` výrazu. Můžete také použít `step` výraz pro Colorizing Heat mapy a rozvrhnout hustotu vizuálně do rozsahů, které se podobají obrysu nebo rozvržení paprskového stylu. Tyto palety barev definují barvy z minima na maximální hodnotu hustoty. 

  Hodnoty barvy pro Heat mapy se určují jako výraz na `heatmap-density` hodnotu. Barva oblasti, kde nejsou definována žádná data na indexu 0 výrazu "interpolace" nebo výchozí barva výrazu "steped". Tuto hodnotu lze použít k definování barvy pozadí. Tato hodnota je často nastavená na transparentní nebo na černou, která je částečně průhledná. 
   
  Tady jsou příklady barevných výrazů:

  | Výraz barvy interpolace | Výraz se zvýrazněnou barvou | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' interpolovat ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' lineární ' \] ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' heatmapu-hustota ' \] ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transparentní",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "fialová",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, ' #fb00fb ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, ' #00c3ff '<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' Step ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' heatmapu-hustota ' \] ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;' transparentní ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, ' námořnická modrá ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, zelená,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "žlutá",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, Red<br/>\] |   

- `opacity`: Určuje, jak je vrstva Heat mapy neprůhledná nebo průhledná.
- `intensity`: Aplikuje násobitel na váhu každého datového bodu pro zvýšení celkové intenzity heatmapu. Způsobuje rozdíl v hmotnosti datových bodů, což usnadňuje vizualizaci.
- `weight`: Ve výchozím nastavení mají všechny datové body váhu 1 a mají stejnou váhu. Možnost váhy funguje jako násobitel a můžete ji nastavit jako číslo nebo výraz. Pokud je číslo nastavené jako váha, je rovnocenným umístěním jednotlivých datových bodů na mapě dvakrát. Pokud je například váha 2, hustota se zdvojnásobí. Nastavení možnosti váhy na číslo vykreslí Heat mapu podobným způsobem, jak použít možnost intenzita. 

  Pokud však použijete výraz, může být váha každého datového bodu založena na vlastnostech jednotlivých datových bodů. Předpokládejme například, že každý datový bod představuje objekt zemětřesení. Hodnota velikosti byla důležitou metrikou pro každý datový bod zemětřesení. K zemětřesení dochází po celou dobu, ale většina má nízkou velikost a nevšimla se. Pomocí hodnoty velikost ve výrazu přiřaďte váhu každému datovému bodu. Když použijete hodnotu velikost pro přiřazení váhy, získáte lepší reprezentaci významnosti zemětřesení v rámci Heat mapy.
- `source` a `source-layer` : umožňují aktualizovat zdroj dat.

Zde je nástroj pro otestování různých možností vrstvy Heat mapy.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy tepelné mapy' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Možnosti vrstvy Heat mapy</a> pera podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konzistentní Heat mapa s přiblížením

Ve výchozím nastavení mají poloměry datových bodů vykreslených v vrstvě Heat mapy u všech úrovní přiblížení pevný obraz v pixelech. Při přiblížení mapy se data agreguje společně a vrstva Heat mapy vypadá jinak. 

Použijte `zoom` výraz pro horizontální navýšení kapacity poloměru pro každou úroveň přiblížení, takže každý datový bod pokrývá stejnou fyzickou oblast mapy. Tento výraz vytvoří vrstvu Heat mapy, která vypadá spolehlivě a je konzistentní. Každá úroveň přiblížení mapy má dvakrát a vodorovně tolik pixelů jako předchozí úroveň přiblížení. 

Škálování poloměru tak, aby dvojnásobek každé úrovně přiblížení vytvoří Heat mapu, která vypadá konzistentně na všech úrovních přiblížení. Chcete-li použít toto škálování, použijte `zoom` se `exponential interpolation` výrazem základní 2 s poloměrem, který je nastavený pro minimální úroveň přiblížení, a s poloměrem na škálované poloměr pro maximální úroveň přiblížení, jak je `2 * Math.pow(2, minZoom - maxZoom)` znázorněno v následující ukázce. Přiblížením mapy zjistíte, jak se Heat mapa škáluje s úrovní přiblížení.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konzistentní Heat mapa s přiblížením" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na CodePen () s Azure Maps použitím <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>lupy s přiblížením na vyměnitelné Heat mapy</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> na <a href='https://codepen.io'></a>.
</iframe>

> [!TIP]
> Pokud povolíte clusteringu na zdroji dat, body, které jsou blízko sebe, jsou seskupeny dohromady jako clusterovaný bod. Počet bodů každého clusteru můžete použít jako výraz váhy pro Heat mapu. To může významně snížit počet bodů, které mají být vykresleny. Počet bodů clusteru je uložený ve `point_count` vlastnosti funkce Point: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Pokud je poloměr clusteringu jenom v několika pixelech, v vykreslování by byl malý vizuální rozdíl. Větší skupiny RADIUS mají více bodů do každého clusteru a zvyšují výkon heatmapu.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [HeatMapLayer](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

Další příklady kódu pro přidání do map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)