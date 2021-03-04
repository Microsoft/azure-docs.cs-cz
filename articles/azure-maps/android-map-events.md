---
title: Zpracování událostí mapy v zařízeních s Androidem Maps | Mapy Microsoft Azure
description: Zjistěte, které události se aktivují, když uživatelé komunikují s mapami. Zobrazí seznam všech podporovaných událostí mapy. Viz jak používat Android SDK Azure Maps ke zpracování událostí.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 86d1b9ec8a507a5cfaa5502efcb239bceabca665
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097342"
---
# <a name="interact-with-the-map-android-sdk"></a>Interakce s mapou (Android SDK)

V tomto článku se dozvíte, jak používat Správce událostí map.

## <a name="interact-with-the-map"></a>Interakce s mapou

Mapa spravuje všechny události prostřednictvím své `events` Vlastnosti. V následující tabulce jsou uvedeny všechny podporované události mapy.

| Událost                  | Formát obslužné rutiny události | Popis |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Je aktivována po posledním snímku vykresleném před tím, než mapování vstoupí do stavu "nečinné":<ul><li>Neprobíhá žádné přechody kamery.</li><li>Všechny aktuálně požadované dlaždice se načetly.</li><li>Všechny animace slábnutí/přechodu se dokončily.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Tato možnost se opakovaně vyvolala během animovaného přechodu z jednoho zobrazení na jiný, jako výsledek interakce uživatele nebo metody. |
| `OnCameraMoveCanceled` | `()`                 | Je aktivována, když byla zrušena žádost o přesun do kamery. |
| `OnCameraMoveStarted`  | `(int reason)`       | Aktivováno těsně před tím, než mapa zahájí přechod z jednoho zobrazení na jiný, jako výsledek interakce uživatele nebo metody. `reason`Argument naslouchacího procesu události vrátí celočíselnou hodnotu, která poskytuje podrobné informace o tom, jak byl zahájen pohyb kamery. Následující seznam popisuje možné důvody:<ul><li>1: gesto</li><li>2: vývojová animace</li><li>3: animace rozhraní API</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | Je aktivována při stisknutí a uvolnění mapy na stejném místě na mapě. |
| `OnFeatureClick`       | `(List<Feature>)`    | Je aktivována, když se mapa stiskne a uvolní ve stejném bodě funkce.  |
| `OnLayerAdded` | `(Layer layer)` | Je aktivována, když dojde k přidání vrstvy do mapy. |
| `OnLayerRemoved` | `(Layer layer)` | Je aktivována, když dojde k odebrání vrstvy z mapy. |
| `OnLoaded` | `()` | Je aktivována ihned po stažení všech potřebných prostředků a prvním vizuálním dokončením vykreslování mapy. |
| `OnLongClick`          | `(double lat, double lon)` | Je aktivována, když je mapa stisknuta, je držena za chvíli a pak byla uvolněna na stejném místě na mapě. |
| `OnLongFeatureClick `  | `(List<Feature>)`    | Je aktivována, když je mapa stisknuta, je držena za chvíli a pak byla uvolněna na stejný bod funkce. |
| `OnReady`              | `(AzureMap map)`     | Je aktivována, když je nanačtena mapa zpočátku, nebo když se změní orientace aplikace a načtou se minimální požadované prostředky mapy a mapa je připravena k programové interakci. |
| `OnSourceAdded` | `(Source source)` | Je aktivována, `DataSource` Když `VectorTileSource` dojde k přidání nebo k mapě. |
| `OnSourceRemoved` | `(Source source)` | Je aktivována, když dojde k `DataSource` `VectorTileSource` Odebrání nebo z mapy. |
| `OnStyleChange` | `()` | Je aktivována, když styl mapy načítá nebo mění. |

Následující kód ukazuje, jak přidat `OnClick` `OnFeatureClick` události, a `OnCameraMove` do mapy.

::: zone pivot="programming-language-java-android"

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.events.add(OnClick { lat: Double, lon: Double -> 
    //Map clicked.
})

map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    //Feature clicked.
})

map.events.add(OnCameraMove {
    //Map camera moved.
})
```

::: zone-end

Další informace najdete v dokumentaci k [mapě](how-to-use-android-map-control-library.md#navigating-the-map) týkající se interakce s mapou a triggerem událostí.

## <a name="scope-feature-events-to-layer"></a>Rozsah událostí funkcí na vrstvu

Při přidávání `OnFeatureClick` událostí nebo `OnLongFeatureClick` do mapy lze instanci vrstvy nebo ID vrstvy předat jako druhý parametr. Když je předána vrstva, událost se aktivuje pouze v případě, že dojde k události v této vrstvě. Události, které jsou v rozsahu pro vrstvy, jsou podporovány pomocí vrstev symbol, bublinová, Spojnicová a mnohoúhelník.

::: zone pivot="programming-language-java-android"

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.
}, layer);

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source.
val source = DataSource()
map.sources.add(source)

//Add data to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a layer and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnFeatureClick { features: List<Feature?>? -> 
        //One or more features clicked.
    },
    layer
)

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnLongFeatureClick { features: List<Feature?>? -> 
         //One or more features long clicked.
    },
    layer
)
```

::: zone-end

## <a name="next-steps"></a>Další kroky

Úplné příklady kódu najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Navigace v mapě](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](how-to-add-shapes-to-android-map.md)
