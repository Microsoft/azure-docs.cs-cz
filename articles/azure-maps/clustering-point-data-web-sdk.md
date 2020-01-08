---
title: Data bodu clusteringu v Azure Maps | Microsoft Docs
description: Postup pro data bodu clusteru v sadě web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 56d9a9a629e64430c97cf392ee4381e1ad7ca906
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433033"
---
# <a name="clustering-point-data"></a>Data bodu clusteringu

Když vizualizujete řadu datových bodů na mapě, navzájem se navzájem překrývají mapa, zdá se, že mapa vypadá a že je obtížné ji zobrazit a použít. Pro zlepšení činnosti koncového uživatele lze použít clusteringu dat bodu. Data bodu clusteringu je proces kombinování bodových dat blízko sebe a jejich reprezentace na mapě jako jeden clusterovaný datový bod. Když se uživatel přiblíží k mapě, rozdělí se clustery do jednotlivých datových bodů.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Povolení clusteringu u zdroje dat

Clustering se dá snadno povolit u třídy `DataSource` nastavením možnosti `cluster` na true (pravda). Kromě toho se dá poloměr v pixelech vybrat okolní body, které se mají zkombinovat do clusteru, nastavit pomocí `clusterRadius` a na základě možnosti `clusterMaxZoom` se dá určit úroveň přiblížení, na které se má zakázat logika clusteringu. Tady je příklad, jak povolit clusteringu ve zdroji dat.

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
> Pokud jsou dva datové body blízko sebe na zemi, je možné, že cluster nebude nikdy rozdělen bez ohledu na to, jak se uživatel blíží. Chcete-li to vyřešit, můžete nastavit možnost `clusterMaxZoom` zdroje dat, která určuje na úrovni přiblížení, chcete-li zakázat logiku clusteringu a jednoduše zobrazit vše.

Třída `DataSource` má také následující metody týkající se clusteringu:

| Metoda | Návratový typ | Popis |
|--------|-------------|-------------|
| getClusterChildren (clusterId: Number) | &lt;funkce&lt;Promise&lt;geometrii, všechny&gt; \| tvar&gt;&gt; | Načte podřízené objekty daného clusteru na další úrovni přiblížení. Tyto podřízené položky mohou být kombinací tvarů a podclusterů. Podclustery budou funkcemi s vlastnostmi, které odpovídají ClusteredProperties. |
| getClusterExpansionZoom (clusterId: Number) | Číslo&lt;Promise&gt; | Vypočítá úroveň přiblížení, při které se cluster začne zvětšovat nebo rozdělovat. |
| getClusterLeaves (clusterId: Number; limit: Number; offset: Number) | &lt;funkce&lt;Promise&lt;geometrii, všechny&gt; \| tvar&gt;&gt; | Načte všechny body v clusteru. Nastavte `limit` pro vrácení podmnožiny bodů a použijte `offset` ke stránkám prostřednictvím bodů. |

## <a name="display-clusters-using-a-bubble-layer"></a>Zobrazení clusterů pomocí bublinové vrstvy

Bublinová vrstva je skvělým způsobem, jak vykreslovat clusterované body, protože můžete snadno škálovat poloměr a měnit jejich barvu na základě počtu bodů v clusteru pomocí výrazu. Při zobrazování clusterů pomocí bublinové vrstvy byste měli použít také samostatnou vrstvu pro vykreslování neclusterovaných datových bodů. Často je dobré zobrazit také velikost clusteru nad bublinami. K dosažení tohoto chování lze použít vrstvu symbolů s textem a žádnou ikonou. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Základní clustery s bublinovou vrstvou" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
V <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>clustery s bublinovou vrstvou</a> pera na základní úrovni.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Zobrazení clusterů pomocí vrstvy symbolů

Při vizualizaci dat bodu pomocí vrstvy symbolů ve výchozím nastavení automaticky skryje symboly, které se vzájemně překrývají, aby bylo možné vytvořit čisticí prostředí. to však nemusí být žádoucí, pokud chcete zobrazit hustotu datových bodů na mapě. Nastavení `allowOverlap` možnosti `iconOptions` vlastností symbolu vrstvy `true` zakáže toto prostředí, ale výsledkem bude zobrazení všech symbolů. Použití clusteringu vám umožňuje zobrazit hustotu všech dat při vytváření skvělého uživatelského prostředí. V této ukázce se vlastní symboly budou používat k reprezentaci clusterů a jednotlivých datových bodů.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clusterovaný Symbolová vrstva" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
V CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'></a>se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>vrstvu se clusterovaným symbolem</a> pera.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Vytváření clusterů a vrstva Heat mapy

Heat mapy představují skvělý způsob, jak zobrazit hustotu dat na mapě. Tato vizualizace může zpracovávat velký počet datových bodů samostatně, ale může zpracovávat ještě více dat, pokud jsou datové body clusterované a velikost clusteru se používá jako váha Heat mapy. Nastavte `weight` možnosti vrstvy Heat mapy na `['get', 'point_count']` k tomuto účelu. Pokud je poloměr clusteru malý, bude Heat mapa téměř totožná s Heat mapou pomocí neclusterovaných datových bodů, ale bude to mnohem lepší. Ale menší poloměr clusteru, tím přesnější bude Heat mapa, ale s menší výhodou výkonu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vážená Heat mapa clusteru" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na CodePen (@azuremaps) na <a href='https://codepen.io'></a>(<a href='https://codepen.io/azuremaps'> </a>) na základě <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>váženého Heat Azure Maps mapy clusteru</a> pera.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Události myši v clusterovaných datových bodech

Když dojde k událostem myši na vrstvě, která obsahuje seskupené datové body, bude se do události vracet skupinový datový bod jako objekt funkce bodu injson. Tato funkce Point bude mít následující vlastnosti:

| Název vlastnosti             | Typ    | Popis   |
|---------------------------|---------|---------------|
| `cluster`                 | Boolean | Indikuje, že funkce představuje cluster. |
| `cluster_id`              | string  | Jedinečné ID clusteru, které lze použít s metodami DataSource `getClusterExpansionZoom`, `getClusterChildren`a `getClusterLeaves`. |
| `point_count`             | číslo  | Počet bodů, které cluster obsahuje.  |
| `point_count_abbreviated` | string  | Řetězec, který zkrátí `point_count` hodnotu, pokud je Long. (například 4 000 se bude 4K)  |

Tento příklad přebírá bublinovou vrstvu, která vykresluje body clusteru a přidává událost Click, která se aktivuje, vypočítá a přiblíží mapu k další úrovni přiblížení, při které se cluster rozdělí, pomocí metody `getClusterExpansionZoom` `DataSource` třídy a vlastnosti `cluster_id` v kliknutí na clusterovaný datový bod. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom clusteru" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na Azure Maps <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na CodePen (pero) na <a href='https://codepen.io'></a>.
</iframe>

## <a name="display-cluster-area"></a>Zobrazit oblast clusteru 

Data bodu, která cluster představuje, jsou rozložena v oblasti. V této ukázce když je ukazatel myši umístěn na clusteru, budou použity jednotlivé datové body, které obsahuje (opustí) k výpočtu vypouklé trupu a zobrazeného na mapě k zobrazení oblasti. Všechny body obsažené v clusteru lze načíst ze zdroje dat pomocí metody `getClusterLeaves`. Vypuklý trup je mnohoúhelník, který zabalí sadu bodů jako elastický pruh a dá se vypočítat pomocí metody `atlas.math.getConvexHull`.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Vypuklý trup oblasti clusteru" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>oblast clusteru pera vypuklý trup</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregace dat v clusterech

Clustery se často reprezentují pomocí symbolu s počtem bodů, které jsou v clusteru, ale v některých případech je ale žádoucí, aby bylo možné lépe přizpůsobit styl clusterů na základě některé metriky, jako je celkový počet tržeb všech bodů v clusteru. V případě agregačních vlastností clusterů lze vytvořit a naplnit pomocí výpočtu [agregačního výrazu](data-driven-style-expressions-web-sdk.md#aggregate-expression) .  Agregace clusterů lze definovat v `clusterProperties` možnosti `DataSource`.

Následující příklad používá agregační výraz pro výpočet počtu na základě vlastnosti typ entity každého datového bodu v clusteru.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregace clusteru" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>agregace clusteru</a> pera pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [DataSource – třída](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Objekt DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas. Math – obor názvů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Další funkce pro přidání funkcí do aplikace najdete v příkladech kódu:

> [!div class="nextstepaction"]
> [Přidat bublinovou vrstvu](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Přidat vrstvu symbolů](map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidat vrstvu Heat mapy](map-add-heat-map-layer.md)
