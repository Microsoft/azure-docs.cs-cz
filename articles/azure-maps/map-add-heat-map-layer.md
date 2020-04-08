---
title: Přidání vrstvy tepelné mapy do mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak přidat vrstvu tepelné mapy do mapy pomocí sady Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 19765bd28f365cc6f6d5b06646896613dd3e3e87
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804550"
---
# <a name="add-a-heat-map-layer"></a>Přidání vrstvy heat mapy

Tepelné mapy, známé také jako mapy hustoty bodů, jsou typem vizualizace dat. Používají se k reprezentaci hustoty dat pomocí rozsahu barev a zobrazení dat "hot spots" na mapě. Tepelné mapy jsou skvělý způsob, jak vykreslit datové sady s velkým počtem bodů. 

Vykreslování desítek tisíc bodů jako symbolů může pokrýt většinu oblasti mapy. Tento případ pravděpodobně vede k překrývání mnoha symbolů. Ztěžuje lepší pochopení dat. Vizualizace stejné datové sady jako tepelné mapy však usnadňuje zobrazení hustoty a relativní hustoty každého datového bodu.

Tepelné mapy můžete použít v mnoha různých scénářích, včetně:

- **Údaje o teplotě**: Poskytuje aproximace pro to, jaká je teplota mezi dvěma datovými body.
- **Data pro snímače šumu**: Zobrazuje nejen intenzitu šumu, kde je snímač, ale může také poskytnout vhled do rozptylu na dálku. Hladina hluku v jednom místě nemusí být vysoká. Pokud se oblast pokrytí hlukem z více senzorů překrývá, je možné, že tato překrývající se oblast může zaznamenat vyšší hladinu hluku. Jako taková by překrývající se oblast byla viditelná v tepelné mapě.
- **Trasování GPS**: Zahrnuje rychlost jako mapu vážené výšky, kde je intenzita každého datového bodu založena na rychlosti. Tato funkce například poskytuje způsob, jak zjistit, kde vozidlo jenom jenom.

> [!TIP]
> Vrstvy tepelné mapy ve výchozím nastavení vykreslují souřadnice všech geometrií ve zdroji dat. Chcete-li vrstvu omezit tak, aby vykreslovala pouze prvky geometrie bodů, nastavte `filter` vlastnost hladiny na `['==', ['geometry-type'], 'Point']`. Pokud chcete zahrnout také funkce Vícebodového `filter` bodu, nastavte `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`vlastnost vrstvy na .

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Přidání vrstvy heat mapy

Chcete-li vykreslit zdroj dat bodů jako tepelnou mapu, `HeatMapLayer` předejte zdroj dat do instance třídy a přidejte jej do mapy.

V následujícím kódu má každý tepelný bod poloměr 10 pixelů při všech úrovních přiblížení. Aby bylo zajištěno lepší uživatelské prostředí, je tepelná mapa pod vrstvou popisku. Štítky zůstávají jasně viditelné. Data v tomto vzorku je z [USGS zemětřesení nebezpečí Program](https://earthquake.usgs.gov/). To je pro významné zemětřesení, ke kterým došlo v posledních 30 dnech.

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

Tady je kompletní ukázka spuštěného kódu předchozího kódu.

<br/>

<iframe height='500' scrolling='no' title='Jednoduchá vrstva tepelné mapy' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>pero jednoduché vrstvy tepla mapy</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Přizpůsobení vrstvy tepelné mapy

Předchozí příklad přizpůsobil tepelnou mapu nastavením možností poloměru a krytí. Vrstva tepelné mapy poskytuje několik možností přizpůsobení, včetně:

* `radius`: Definuje poloměr v obrazových bodech, ve kterém se mají jednotlivé datové body vykreslit. Poloměr můžete nastavit jako pevné číslo nebo jako výraz. Pomocí výrazu můžete změnit měřítko poloměru na základě úrovně přiblížení a představovat konzistentní prostorovou oblast na mapě (například poloměr 5 mil).
* `color`: Určuje, jak je tepelná mapa obarvena. Barevný gradient je běžným rysem tepelných map. Efekt můžete dosáhnout výrazem. `interpolate` Můžete také použít `step` výraz pro vybarvení tepelné mapy a vizuální rozdělení hustoty na rozsahy, které se podobají obrysové nebo radarové mapě stylu. Tyto palety barev definují barvy od minimální hodnoty maximální hustoty. 

  Hodnoty barev pro tepelné mapy určíte `heatmap-density` jako výraz pro hodnotu. Barva oblasti, kde nejsou žádná data, je definována v indexu 0 výrazu "Interpolace" nebo v výchozí barvě výrazu "Stepovaný". Tuto hodnotu můžete použít k definování barvy pozadí. Tato hodnota je často nastavena na průhlednou nebo poloprůhlednou černou. 
   
  Zde jsou příklady barevných výrazů:

  | Rezortační barevný výraz | Stupňovitý barevný výraz | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpolovat",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["lineární"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["hustotou heatmap"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transparentní",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, "fialová",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"krok",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["hustotou heatmap"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"transparentní",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "námořnictvo",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, "zelená",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "žlutá",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "červená"<br/>\] |   

- `opacity`: Určuje, jak neprůhledná nebo průhledná je vrstva tepelné mapy.
- `intensity`: Použije násobitel na hmotnost každého datového bodu, aby se zvýšila celková intenzita heatmapy. To způsobuje rozdíl v hmotnosti datových bodů, což usnadňuje vizualizaci.
- `weight`: Ve výchozím nastavení mají všechny datové body váhu 1 a jsou váženy stejně. Možnost tloušťky funguje jako násobitel a můžete ji nastavit jako číslo nebo výraz. Pokud je číslo nastaveno jako hmotnost, je to ekvivalence umístění každého datového bodu na mapě dvakrát. Například, pokud je hmotnost 2, pak se hustota zdvojnásobí. Nastavením možnosti hmotnosti na číslo se vykreslí tepelná mapa podobným způsobem jako použití možnosti intenzity. 

  Pokud však použijete výraz, může být hmotnost každého datového bodu založena na vlastnostech každého datového bodu. Předpokládejme například, že každý datový bod představuje zemětřesení. Hodnota velikosti byla důležitou metrikou pro každý datový bod zemětřesení. Zemětřesení se dějí po celou dobu, ale většina z nich má nízkou velikost, a nejsou si všiml. Pomocí hodnoty velikosti ve výrazu přiřaďte váhu ke každému datovému bodu. Použitím hodnoty velikosti přiřadit hmotnost, získáte lepší reprezentaci významu zemětřesení v rámci tepelné mapy.
- `source`a `source-layer`: Umožňuje aktualizovat zdroj dat.

Zde je nástroj pro testování různých možností vrstvy tepelné mapy.

<br/>

<iframe height='700' scrolling='no' title='Volby vrstvy tepelné mapy' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>možnosti vrstvy heat mapy</a> pera podle Map Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konzistentní zoomovatelná tepelná mapa

Ve výchozím nastavení mají poloměry datových bodů vykreslené ve vrstvě tepelné mapy pevný poloměr obrazových bodů pro všechny úrovně zvětšení. Při přiblížení mapy se data agregují a vrstva tepelné mapy vypadá jinak. 

Pomocí `zoom` výrazu můžete změnit měřítko poloměru pro každou úroveň přiblížení tak, aby každý datový bod pokrýval stejnou fyzickou oblast mapy. Tento výraz způsobí, že vrstva tepelné mapy bude vypadat statickyji a konzistentněji. Každá úroveň přiblížení mapy má dvakrát tolik obrazových bodů svisle a vodorovně než předchozí úroveň přiblížení. 

Změna poloměru tak, aby se zdvojnásobila s každou úrovní zvětšení, vytvoří tepelnou mapu, která bude vypadat konzistentně na všech úrovních přiblížení. Chcete-li použít toto měřítko, použijte se `zoom` `exponential interpolation` základním 2 výrazem s poloměrem obrazových bodů nastaveným `2 * Math.pow(2, minZoom - maxZoom)` pro minimální úroveň zvětšení a poloměrem zmenšeného měřítka pro maximální úroveň zvětšení vypočtenou podle následujícíukázky. Zvětšete mapu, abyste viděli, jak se měří tepelná mapa s úrovní přiblížení.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konzistentní zoomovatelná tepelná mapa" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mapu pera konzistentní zoomovatelné teplo</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Pokud povolíte clustering ve zdroji dat, body, které jsou blízko sebe, jsou seskupeny jako clusterovaný bod. Počet bodů každého clusteru můžete použít jako výraz hmotnosti pro tepelnou mapu. To může výrazně snížit počet bodů, které mají být vykresleny. Počet bodů clusteru je uložen `point_count` ve vlastnosti funkce bodu: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Pokud je poloměr clusteringu pouze několik pixelů, bude v rendrování malý vizuální rozdíl. Větší poloměr seskupí více bodů do každého clusteru a zlepší výkon heatmap.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Další příklady kódu, které chcete přidat do map, najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)
