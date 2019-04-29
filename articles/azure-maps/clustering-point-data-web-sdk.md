---
title: Clustering data bodu ve službě Azure Maps | Dokumentace Microsoftu
description: Tom, jak data bodu clusteru v sadě Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d4dc6f0c8fd2dff74a1997c9dca5a31abc70c03a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795932"
---
# <a name="clustering-point-data"></a>Clustering datového bodu

Při vizualizaci mnoho datových bodů na mapě, body překrývají, mapa vypadá nevypadala a bude obtížné zobrazovat a používat. Clustering bod dat je možné na zdokonalování tohoto prostředí uživatele. Clustering bod dat je proces kombinování dat bodu, která jsou blízko sebe a představující na mapě jako Clusterované jednoho datového bodu. Jako uživatel přiblíží mapy, clustery rozdělit do jejich jednotlivých datových bodů.

## <a name="enabling-clustering-on-a-data-source"></a>Povolíte clustering ve zdroji dat.

Clustering snadno lze povolit `DataSource` třídy tak, že nastavíte `cluster` možnost na hodnotu true. Kromě toho pixel radius vyberte okolních bodů ke sloučení do clusteru lze nastavit pomocí `clusterRadius` a úroveň zvětšení je možné zadat, kam chcete zakázat clusteringu pomocí logiky `clusterMaxZoom` možnost. Tady je příklad toho, jak na zapnutí clusterování ve zdroji dat.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximium zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> Pokud dva datové body jsou blízko sebe na místě, je možné, že cluster se nikdy rozdělit, bez ohledu na to, jak blízko zvětší uživatele v. Chcete-li to vyřešit, můžete nastavit `clusterMaxZoom` možnost zdroj dat, které určuje na úroveň přiblížení pro zakázání clusteringu logiky a jednoduše zobrazit všechno, co.

`DataSource` Třída také obsahuje následující metody související s clusterem:

| Metoda | Návratový typ | Popis |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promise&lt;funkce&lt;geometrie, všechny&gt; \| obrazce&gt; | Načte podřízených objektů daného clusteru na další úroveň přiblížení. Tyto podřízené objekty může být kombinací obrazců a subclusters. Subclusters bude funkce s vlastnostmi odpovídající ClusteredProperties. |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;číslo&gt; | Vypočítá úroveň přiblížení, ve kterém bude clusteru spusťte rozšíření nebo rozdělit. |
| getClusterLeaves (clusterId: čísla, limit: čísla, posun: číslo) | Promise&lt;funkce&lt;geometrie, všechny&gt; \| obrazce&gt; | Načte všechny body v clusteru. Nastavte `limit` vrátí podmnožinu body a použít `offset` na stránku body. |

## <a name="display-clusters-using-a-bubble-layer"></a>Zobrazit clustery pomocí bublinu vrstvy.

Vrstva bublinu je skvělý způsob, jak vykreslit Clusterované body můžete snadno škálovat protokolu radius a změnit barvu, je založena na počet bodů v clusteru pomocí výrazu. Při zobrazení clustery pomocí vrstvy bublin, byste měli také použít samostatné vrstvy pro vykreslení ke zrušení datových bodů. Často je dobré si také moct zobrazit velikost clusteru nad bubliny. Symbol vrstvu s textem a žádná ikona lze použít k dosažení tohoto chování. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Základní bublinu vrstvy clustering" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobrazit pera <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>základní bublinu vrstvy clustering</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Clustery zobrazení pomocí symbolu vrstvy.

Když vizualizaci bod dat pomocí vrstvy Symbol ve výchozím nastavení se budou automaticky skrýt symboly tohoto překrytí vzájemně k vytvoření prostředí přehlednější, ale toto video asi požadované prostředí. Pokud chcete zobrazit hustota dat odkazuje na mapě. Nastavení `allowOverlap` možnost vrstvy Symbol `iconOptions` vlastnost `true` zakáže toto prostředí ale bude mít za následek všechny symboly se zobrazí. Použití clusteringu s umožňuje zobrazit hustotu nad všemi daty při vytváření nice čisté uživatelské prostředí. V této ukázce se použije vlastní symboly pro vyjádření clusterů a jednotlivých datových bodů.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clusterované Symbol vrstvy" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobrazit pera <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>vrstvy clusteru Symbol</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering a heat mapy vrstvy

Heat mapy jsou skvělý způsob, jak zobrazit hustota data na mapě. Tato vizualizace může zpracovávat velký počet datových bodů sama o sobě, ale zvládne ještě více dat, pokud jsou Clusterované datové body a velikost clusteru se použije jako váhu heat mapa. Nastavte `weight` možnost vrstva heat mapa `['get', 'point_count']` toho dosáhnout. Při malém clusteru pomocí protokolu radius heat mapa bude vypadat téměř shodné s heat mapa používání ke zrušení datových bodů, ale provede mnohem lépe. Však Čím menší radius clusteru, přesnější heat mapa se ale s menší výkon výhody.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Váha Heat mapa clusteru" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobrazit pera <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>clusteru váha Heat mapa</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Události myši na Clusterované datových bodů

Při výskytu události myši na vrstvu, která obsahují clusterovaná data body, bude vrácen bodu clusterovaná data události jako objekt GeoJSON bodu funkce. Tato funkce bodu bude mít následující vlastnosti:

| Název vlastnosti | Type | Popis |
|---------------|------|-------------|
| Cluster | Boolean | Označuje, pokud funkce představuje cluster. |
| cluster_id | string | Jedinečné ID pro cluster, který jde použít s zdroj dat `getClusterExpansionZoom`, `getClusterChildren`, a `getClusterLeaves` metody. |
| point_count | číslo | Počet bodů, které obsahuje cluster. |
| point_count_abbreviated | string | Řetězec, který se zkrátí point_count hodnotu, pokud je dlouhý. (například 4 000 stane 4 kB) |

V tomto příkladu přebírá vrstvu bublinu, která vykreslí body clusteru a přidá událost click, která při aktivaci, vypočítat a přiblížení mapy na další úroveň přiblížení, jakou clusteru přeruší od sebe pomocí `getClusterExpansionZoom` metodu `DataSource` třídy a `cluster_id` vlastnost kliknutí na datový bod v clusteru. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom clusteru" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobrazit pera <a href='https://codepen.io/azuremaps/pen/moZWeV/'>clusteru getClusterExpansionZoom</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Umožňuje zobrazit oblast clusteru 

Data bodu, který představuje cluster je rozdělena do oblasti. V této ukázce, když je nastavená myš clusteru, jednotlivé datové body, které že obsahuje (listy) se používá k výpočtu konvexní trupu a zobrazená na mapě zobrazíte v oblasti. Všechny body, které jsou obsaženy v clusteru je načíst ze zdroje dat s použitím `getClusterLeaves` metody. Konvexní trupu je mnohoúhelníku, která obaluje sadu body, jako je elastický a je možné vypočítat pomocí `atlas.math.getConvexHull` metody.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Cluster oblasti konvexní trupu" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobrazit pera <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>clusteru oblasti konvexní trupu</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Třída zdroje dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions musí objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [obor názvů Atlas.Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Příklady kódu pro přidání funkce do vaší aplikace:

> [!div class="nextstepaction"]
> [Přidat vrstvu bublinový](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Přidat vrstvu symbol](map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidat vrstvu heat mapy](map-add-heat-map-layer.md)
