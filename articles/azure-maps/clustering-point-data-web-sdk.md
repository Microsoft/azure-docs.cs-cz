---
title: Data bodů clusteru na mapě | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak sdružovat data bodů a vykreslit je na mapě pomocí sady Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: ce2891201331ee1efd861d2f13cec78c0551b6ba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804567"
---
# <a name="clustering-point-data"></a>Data bodů clusteru

Při vizualizaci mnoha datových bodů na mapě se mohou datové body překrývat mezi sebou. Překrytí může způsobit, že se mapa může stát nečitelnou a obtížně použitelnou. Data bodů clusteringu je proces kombinování bodových dat, která jsou blízko sebe a představují je na mapě jako jeden clusterovaný datový bod. Jak se uživatel přiblíží k mapě, clustery se rozpadají na své jednotlivé datové body. Při práci s velkým počtem datových bodů, použijte clustering procesy ke zlepšení uživatelského prostředí.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Povolení clusteringu ve zdroji dat

Povolit clustering `DataSource` ve třídě `cluster` nastavením možnosti true. Nastavte `ClusterRadius` na výběr blízkých bodů a zkombinuje je do clusteru. Hodnota `ClusterRadius` je v pixelech. Slouží `clusterMaxZoom` k určení úrovně zvětšení, při které chcete zakázat logiku clusteringu. Zde je příklad, jak povolit clustering ve zdroji dat.

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
> Pokud jsou dva datové body blízko u sebe na zemi, je možné, že se cluster nikdy nerozpadne, bez ohledu na to, jak blízko uživatel přiblíží. Chcete-li tento problém `clusterMaxZoom` vyřešit, můžete nastavit možnost zakázat logiku clusteringu a jednoduše zobrazit vše.

Zde jsou další `DataSource` metody, které poskytuje třídy pro clustering:

| Metoda | Návratový typ | Popis |
|--------|-------------|-------------|
| getClusterChildren(clusterId: číslo) | Geometrie&lt;prvku pole&gt; \| &lt;&lt;Slib, libovolný tvar&gt;&gt; | Načte podřízené děti daného clusteru na další úrovni přiblížení. Tyto podřízené položky mohou být kombinací tvarů a podclusterů. Podclustery budou funkce s vlastnostmi odpovídající clusteredProperties. |
| getClusterExpansionZoom(clusterId: číslo) | Číslo&lt;příslibu&gt; | Vypočítá úroveň zvětšení, při které se cluster začne rozšiřovat nebo rozpadat. |
| getClusterLeaves(clusterId: číslo, limit: číslo, posun: číslo) | Geometrie&lt;prvku pole&gt; \| &lt;&lt;Slib, libovolný tvar&gt;&gt; | Načte všechny body v clusteru. Nastavte `limit` pro vrácení podmnožiny bodů a `offset` použijte stránku procházet body. |

## <a name="display-clusters-using-a-bubble-layer"></a>Zobrazení clusterů pomocí bublinové vrstvy

Bublinová vrstva je skvělý způsob, jak vykreslit seskupené body. Pomocí výrazů můžete změnit měřítko poloměru a změnit barvu na základě počtu bodů v clusteru. Pokud clustery zobrazujete pomocí bublinové vrstvy, měli byste použít samostatnou vrstvu k vykreslení neseskupených datových bodů.

Chcete-li zobrazit velikost clusteru nad bublinou, použijte vrstvu symbolů s textem a nepoužívejte ikonu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Základní shlukování bublinových vrstev" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>clustering bublinové vrstvy</a> pera Základní podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Zobrazení clusterů pomocí vrstvy symbolů

Při vizualizaci datových bodů vrstva symbolů automaticky skryje symboly, které se navzájem překrývají, aby bylo zajištěno čistší uživatelské rozhraní. Toto výchozí chování může být nežádoucí, pokud chcete zobrazit hustotu datových bodů na mapě. Tato nastavení však lze změnit. Chcete-li zobrazit všechny `allowOverlap` symboly, `iconOptions` nastavte `true`volbu vlastnosti Symbol vrstvy na . 

Pomocí clusteringu můžete zobrazit hustotu datových bodů při zachování čistého uživatelského rozhraní. Následující ukázka ukazuje, jak přidat vlastní symboly a reprezentovat clustery a jednotlivé datové body pomocí vrstvy symbolů.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vrstva seskupených symbolů" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>vrstvu Pseudonym</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>ový symbol podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Shlukování a vrstva tepelných map

Tepelné mapy jsou skvělým způsobem, jak zobrazit hustotu dat na mapě. Tato metoda vizualizace může zpracovat velký počet datových bodů samostatně. Pokud jsou datové body seskupeny a velikost clusteru se používá jako hmotnost tepelné mapy, pak tepelná mapa zvládne ještě více dat. Chcete-li této možnosti dosáhnout, nastavte `weight` `['get', 'point_count']`volbu vrstvy tepelné mapy na . Když je poloměr clusteru malý, bude tepelná mapa vypadat téměř stejně jako tepelná mapa pomocí neseskupených datových bodů, ale bude fungovat mnohem lépe. Čím menší je však poloměr clusteru, tím přesnější bude tepelná mapa, ale s menšími výhodami výkonu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa tepla vážená klastrem" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>upevňovaná tepelná mapa</a> clusteru pera podle Map Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>codepenu</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Události myši v clusterovaných datových bodech

Pokud dojde k událostem myši ve vrstvě, která obsahuje clusterované datové body, clusterovaný datový bod se vrátí k události jako objekt prvku bodu GeoJSON. Tento bodový prvek bude mít následující vlastnosti:

| Název vlastnosti             | Typ    | Popis   |
|---------------------------|---------|---------------|
| `cluster`                 | Boolean | Označuje, zda funkce představuje cluster. |
| `cluster_id`              | řetězec  | Jedinečné ID clusteru, které lze použít `getClusterExpansionZoom`se `getClusterChildren`zdrojem dat , a `getClusterLeaves` metodami. |
| `point_count`             | číslo  | Počet bodů, které cluster obsahuje.  |
| `point_count_abbreviated` | řetězec  | Řetězec, který zkracuje `point_count` hodnotu, pokud je dlouhá. (například 4 000 se změní na 4K)  |

Tento příklad přebírá bublinovou vrstvu, která vykresluje body clusteru a přidává událost kliknutí. Když se událost kliknutí aktivuje, kód vypočítá a přiblíží mapu na další úroveň přiblížení, při které se cluster rozpadne. Tato funkce je implementována pomocí `getClusterExpansionZoom` metody `DataSource` třídy a `cluster_id` vlastností klepnunutého clusterovaného datového bodu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/moZWeV/'>pero clusteru getClusterExpansionZoom</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Zobrazit oblast clusteru 

Bodová data, která představuje cluster, jsou rozložena do oblasti. V této ukázce, když je myš je vznášet nad clusteru, dojde ke dvěma hlavním chováním. Za prvé, jednotlivé datové body obsažené v clusteru budou použity k výpočtu konvexního trupu. Poté se na mapě zobrazí konvexní korbor, který zobrazí oblast.  Konvexní trup je polygon, který zabalí sadu bodů, jako `atlas.math.getConvexHull` je elastický pás, a lze jej vypočítat pomocí metody. Všechny body obsažené v clusteru lze načíst ze `getClusterLeaves` zdroje dat pomocí metody.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Konvexní trup oblasti clusteru" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>konvexní konvexní trup oblasti clusteru</a> pera podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregace dat v clusterech

Clustery jsou často reprezentovány pomocí symbolu s počtem bodů, které jsou v rámci clusteru. Někdy je však žádoucí přizpůsobit styl clusterů pomocí dalších metrik. Pomocí agregace clusteru lze vytvořit a naplnit vlastní vlastnosti pomocí výpočtu [agregačního výrazu.](data-driven-style-expressions-web-sdk.md#aggregate-expression)  Agregace clusteru `clusterProperties` lze `DataSource`definovat v možnosti .

Následující ukázka používá agregační výraz. Kód vypočítá počet na základě vlastnosti typu entity každého datového bodu v clusteru. Když uživatel klikne na clusteru, zobrazí se vyskakovací okno s dalšími informacemi o clusteru.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregace clusteru" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>agregace clusteru</a> pera podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Třída DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Objekt DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [obor názvů atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Podívejte se na příklady kódu pro přidání funkcí do aplikace:

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy heat mapy](map-add-heat-map-layer.md)
