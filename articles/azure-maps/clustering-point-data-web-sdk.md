---
title: Data bodu clusteringu na mapě | Mapy Microsoft Azure
description: Naučte se, jak odkazovat na data v clusterech na mapách. Podívejte se, jak použít Azure Maps webové sady SDK pro data v clusteru, reagovat na události myši clusteru a zobrazit agregované clustery.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 9247d6bed8f4ca6ec5e51d0d64eb4fe94dc89af7
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089492"
---
# <a name="clustering-point-data"></a>Data bodu clusteringu

Při vizualizaci řady datových bodů na mapě se mohou datové body překrývat mezi sebou. Překrytí může způsobit, že mapa může být nečitelná a obtížné ji používat. Data bodu clusteringu je proces kombinování bodových dat blízko sebe a jejich reprezentace na mapě jako jeden clusterovaný datový bod. Když se uživatel přiblíží k mapě, rozdělí se clustery do jednotlivých datových bodů. Když pracujete s velkým počtem datových bodů, pomocí procesů clusteringu Vylepšete uživatelské prostředí.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Povolení clusteringu u zdroje dat

Povolte clusteringu ve `DataSource` třídě nastavením `cluster` Možnosti na hodnotu true. Nastavte `ClusterRadius` pro výběr okolních bodů a jejich kombinování do clusteru. Hodnota `ClusterRadius` je v pixelech. Použijte `clusterMaxZoom` k určení úrovně přiblížení, na které se má zakázat logika clusteringu. Tady je příklad, jak povolit clusteringu ve zdroji dat.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Pokud jsou na zemi blízko sebe dva datové body, je možné, že cluster nebude nikdy rozdělen bez ohledu na to, jak se uživatel blíží. K tomuto řešení můžete nastavit `clusterMaxZoom` možnost zakázat logiku clusteringu a jednoduše zobrazit vše.

Tady jsou další metody, které `DataSource` Třída poskytuje pro clusteringu:

| Metoda | Návratový typ | Popis |
|--------|-------------|-------------|
| getClusterChildren (clusterId: Number) | &lt;Geometrie funkce pole promise &lt; &lt; , libovolný &gt; \| tvar&gt;&gt; | Načte podřízené objekty daného clusteru na další úrovni přiblížení. Tyto podřízené položky mohou být kombinací tvarů a podclusterů. Podclustery budou funkcemi s vlastnostmi, které odpovídají ClusteredProperties. |
| getClusterExpansionZoom (clusterId: Number) | Číslo promise &lt;&gt; | Vypočítá úroveň přiblížení, při které se cluster začne zvětšovat nebo rozdělovat. |
| getClusterLeaves (clusterId: Number; limit: Number; offset: Number) | &lt;Geometrie funkce pole promise &lt; &lt; , libovolný &gt; \| tvar&gt;&gt; | Načte všechny body v clusteru. Nastavte, `limit` aby se vracela podmnožina bodů, a použijte `offset` stránku k v bodech. |

## <a name="display-clusters-using-a-bubble-layer"></a>Zobrazení clusterů pomocí bublinové vrstvy

Bublinová vrstva je skvělým způsobem, jak vykreslovat seskupené body. Použijte výrazy pro horizontální navýšení kapacity poloměru a změnu barvy na základě počtu bodů v clusteru. Pokud zobrazíte clustery pomocí bublinové vrstvy, pak byste měli použít samostatnou vrstvu pro vykreslení neclusterovaných datových bodů.

Chcete-li zobrazit velikost clusteru nad bublinou, použijte symbolovou vrstvu s textem a nepoužívejte ikonu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Základní clustery s bublinovou vrstvou" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
V CodePen () na se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>cluster s bublinovou vrstvou základního</a> pera <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Zobrazení clusterů pomocí vrstvy symbolů

Při vizualizaci datových bodů vrstva symbolu automaticky skryje symboly, které se vzájemně překrývají, aby se zajistilo, že bude čisticí uživatelské rozhraní. Toto výchozí chování může být nežádoucí, pokud chcete zobrazit hustotu datových bodů na mapě. Tato nastavení se ale dají změnit. Chcete-li zobrazit všechny symboly, nastavte `allowOverlap` možnost Vlastnosti vrstvy symbolů `iconOptions` na `true` . 

Použití clusteringu k zobrazení hustoty datových bodů při zachování čistého uživatelského rozhraní. Následující ukázka ukazuje, jak přidat vlastní symboly a reprezentovat clustery a jednotlivé datové body pomocí vrstvy symbolů.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clusterovaný Symbolová vrstva" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
V CodePen () na se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>vrstvu se clusterovaným symbolem</a> pera <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Vytváření clusterů a vrstva Heat mapy

Heat mapy představují skvělý způsob, jak zobrazit hustotu dat na mapě. Tato metoda vizualizace může zpracovat velký počet datových bodů sami. Pokud jsou datové body clusterované a velikost clusteru se používá jako váha Heat mapy, pak může Heat mapa zvládnout ještě víc dat. Chcete-li dosáhnout této možnosti, nastavte `weight` možnost vrstvy Heat mapy na `['get', 'point_count']` . Pokud je poloměr clusteru malý, bude Heat mapa téměř totožná s Heat mapou pomocí neclusterovaných datových bodů, ale bude to mít mnohem lepší výkon. Čím menší je ale poloměr clusteru, tím přesnější bude Heat mapa, ale s méně výhodami výkonu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vážená Heat mapa clusteru" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na CodePen () na () <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>vážené Heat Azure Maps mapy</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Události myši v clusterovaných datových bodech

Pokud dojde k událostem myši na vrstvě, která obsahuje seskupené datové body, vrátí se clusterový datový bod do události jako objekt funkce bodu injson. Tato funkce Point bude mít následující vlastnosti:

| Název vlastnosti             | Typ    | Popis   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indikuje, že funkce představuje cluster. |
| `cluster_id`              | řetězec  | Jedinečné ID clusteru, které lze použít s `getClusterExpansionZoom` `getClusterChildren` metodami DataSource, a `getClusterLeaves` . |
| `point_count`             | číslo  | Počet bodů, které cluster obsahuje.  |
| `point_count_abbreviated` | řetězec  | Řetězec, který zkracuje hodnotu, `point_count` Pokud je Long. (například 4 000 se bude 4K)  |

Tento příklad přebírá bublinovou vrstvu, která vykresluje body clusteru a přidává událost Click. Při triggeru události Click kód vypočítá a přiblíží mapu k další úrovni přiblížení, na které se cluster rozdělí. Tato funkce je implementována pomocí `getClusterExpansionZoom` metody `DataSource` třídy a `cluster_id` vlastnosti kliknutí na clusterovaný datový bod.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom clusteru" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Prohlédněte si Azure Maps <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom</a> ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) v <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Zobrazit oblast clusteru 

Data bodu, která cluster představuje, jsou rozložena v oblasti. V této ukázce když je ukazatel myši umístěn na clusteru, dojde k dvou hlavním chování. Nejprve se pro výpočet vypouklé trupu použijí jednotlivé datové body obsažené v clusteru. Pak se na mapě zobrazí vypuklý trup, který zobrazí oblast.  Vypuklý trup je mnohoúhelník, který zabalí sadu bodů jako elastický pruh a lze ji vypočítat pomocí `atlas.math.getConvexHull` metody. Všechny body, které jsou obsaženy v clusteru, lze načíst ze zdroje dat pomocí `getClusterLeaves` metody.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Vypuklý trup oblasti clusteru" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Viz <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>oblast clusteru pera vypuklý trup</a> od Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregace dat v clusterech

Clustery se často reprezentují pomocí symbolu s počtem bodů, které jsou v clusteru. Někdy je ale vhodné přizpůsobit styl clusterů s dalšími metrikami. Pomocí agregovaných hodnot clusteru lze vytvořit vlastní vlastnosti a naplnit je pomocí výpočtu [agregačního výrazu](data-driven-style-expressions-web-sdk.md#aggregate-expression) .  Agregace clusterů lze definovat v `clusterProperties` možnosti `DataSource` .

Následující příklad používá agregační výraz. Kód vypočítá počet založený na vlastnosti typ entity každého datového bodu v clusteru. Když uživatel klikne na cluster, zobrazí se automaticky otevírané okno s dalšími informacemi o clusteru.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregace clusteru" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>agregace clusteru</a> pera pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [DataSource – třída](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [Objekt DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [Atlas. Math – obor názvů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math)

Další funkce pro přidání funkcí do aplikace najdete v příkladech kódu:

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy heat mapy](map-add-heat-map-layer.md)
