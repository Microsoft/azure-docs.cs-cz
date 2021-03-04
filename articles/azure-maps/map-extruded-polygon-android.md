---
title: Přidání vrstvy vysunutí mnohoúhelníku na mapu Androidu | Mapy Microsoft Azure
description: Postup přidání vrstvy vysunutí mnohoúhelníku do Android SDK Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b62c2540dc8cf2c7d4f67d465b2d464cbc0c3091
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054637"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map-android-sdk"></a>Přidání vrstvy vysunutí mnohoúhelníku na mapu (Android SDK)

V tomto článku se dozvíte, jak použít vrstvu vysunutí mnohoúhelníku k vykreslení oblastí `Polygon` a `MultiPolygon` funkcí geometrií jako vytlačených tvarů.

## <a name="use-a-polygon-extrusion-layer"></a>Použití vrstvy vysunutí mnohoúhelníku

Připojte vrstvu vysunutí mnohoúhelníku ke zdroji dat. Pak ho načtete na mapě. Vrstva vysunutí mnohoúhelníku vykresluje oblasti `Polygon` `MultiPolygon` funkcí a jako vytlačené obrazce. `height`Vlastnosti a `base` vrstvy vysunutí mnohoúhelníku definují základní vzdálenost od základu a výšky vytlačeného obrazce v **měřičích**. Následující kód ukazuje, jak vytvořit mnohoúhelník, přidat ho do zdroje dat a vykreslit pomocí třídy vrstev vytlačení mnohoúhelníku.

> [!Note]
> `base`Hodnota definovaná ve vrstvě vysunutí mnohoúhelníku by měla být menší než nebo rovna hodnotě `height` .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.95838379859924, 40.80027995478159),
            Point.fromLngLat(-73.98154735565186, 40.76845986171129),
            Point.fromLngLat(-73.98124694824219, 40.767761062136955),
            Point.fromLngLat(-73.97361874580382, 40.76461637311633),
            Point.fromLngLat(-73.97306084632874, 40.76512830937617),
            Point.fromLngLat(-73.97259950637817, 40.76490890860481),
            Point.fromLngLat(-73.9494466781616, 40.79658450499243),
            Point.fromLngLat(-73.94966125488281, 40.79708807289436),
            Point.fromLngLat(-73.95781517028809, 40.80052360358227),
            Point.fromLngLat(-73.95838379859924, 40.80027995478159)
        )
    )
));

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillColor("#fc0303"),
    fillOpacity(0.7f),
    height(500f)
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

Na následujícím snímku obrazovky vidíte výše narendingý mnohoúhelník, který se svisle roztáhne pomocí vrstvy vysunutí mnohoúhelníku.

![Mapování pomocí mnohoúhelníku roztaženého svisle pomocí vrstvy vysunutí mnohoúhelníku](media/map-extruded-polygon-android/polygon-extrusion-layer.jpg)

## <a name="add-data-driven-polygons"></a>Přidat mnohoúhelníky řízené daty

Mapu kartogramy lze vykreslit pomocí vrstvy vysunutí mnohoúhelníku. Nastavte `height` vlastnosti a `fillColor` vrstvy vytlačení na měření statistické proměnné v `Polygon` `MultiPolygon` geometrií funkcí a. Následující ukázka kódu ukazuje vytlačenou kartogramy mapu USA na základě měření hustoty populace podle stavu.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the geojson data and add it to the data source.
Utils.importData("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/US_States_Population_Density.json", this,  (String result) -> {
    //Parse the data as a GeoJSON Feature Collection.
    FeatureCollection fc = FeatureCollection.fromJson(result);

    //Add the feature collection to the data source.
    source.add(fc);
});

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillOpacity(0.7f),
    fillColor(
        step(
            get("density"),
            literal("rgba(0, 255, 128, 1)"),
            stop(10, "rgba(9, 224, 118, 1)"),
            stop(20, "rgba(11, 191, 103, 1)"),
            stop(50, "rgba(247, 227, 5, 1)"),
            stop(100, "rgba(247, 199, 7, 1)"),
            stop(200, "rgba(247, 130, 5, 1)"),
            stop(500, "rgba(247, 94, 5, 1)"),
            stop(1000, "rgba(247, 37, 5, 1)")
        )
    ),
    height(
        interpolate(
            linear(),
            get("density"),
            stop(0, 100),
            stop(1200, 960000)
        )
    )
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

Na následujícím snímku obrazovky vidíte kartogramy mapu států amerických, která je roztažená a roztažena svisle jako vytlačené mnohoúhelníky na základě hustoty populace.

![Mapa kartogramy Spojených států amerických je barevná a roztažená svisle jako vytlačené mnohoúhelníky na základě hustoty populace.](media/map-extruded-polygon-android/android-extruded-choropleth.jpg)

## <a name="next-steps"></a>Další kroky

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](how-to-add-shapes-to-android-map.md)
