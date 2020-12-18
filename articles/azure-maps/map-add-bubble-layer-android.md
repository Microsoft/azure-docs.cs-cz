---
title: Přidat bublinovou vrstvu do map pro Android | Mapy Microsoft Azure
description: Naučte se vykreslovat body na mapách jako kruhy s pevnými velikostmi. Viz jak použít Android SDK Azure Maps k přidání a přizpůsobení bublinových vrstev pro tento účel.
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 7506a2083a34832ee3f6f6222f86d35d10228728
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681640"
---
# <a name="add-a-bubble-layer-to-a-map-android-sdk"></a>Přidat bublinovou vrstvu na mapu (Android SDK)

V tomto článku se dozvíte, jak vykreslovat data bodů ze zdroje dat jako bublinovou vrstvu na mapě. Bubliny vrstev vykreslí body jako kružnice na mapě s pevným poloměrem v pixelech.

> [!TIP]
> Bublinové vrstvy ve výchozím nastavení budou kreslit souřadnice všech geometrií ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykreslí pouze funkce geometrie bodu, nastavte `filter` možnost vrstvy na `eq(geometryType(), "Point")` . Pokud chcete zahrnout i funkce systému MultiPoint, nastavte `filter` možnost vrstvy na `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Předpoklady

Ujistěte se, že jste dokončili kroky v [rychlém startu: vytvoření dokumentu aplikace pro Android](quick-android-map.md) . Bloky kódu v tomto článku lze vložit do `onReady` obslužné rutiny události Maps.

## <a name="add-a-bubble-layer"></a>Přidání vrstvy bublin

Následující kód načte pole bodů do zdroje dat. Datové body se pak připojí k bublinové vrstvě. Bublinová vrstva vykresluje poloměr každého bublinu s pěti obrazovými body a barvou výplně bílá. A, Barva tahu modrou a šířka tahu šest pixelů.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create point locations.
Point[] points = new Point[] {
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
};

//Add multiple points to the data source.
source.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
BubbleLayer layer = new BubbleLayer(source, 
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
);

map.layers.add(layer);
```

Následující snímek obrazovky ukazuje, že výše uvedený kód vykresluje body v bublinové vrstvě.

![Mapovat s body vykreslenými pomocí bublinové vrstvy](media/map-add-bubble-layer-android/android-bubble-layer.png)

## <a name="show-labels-with-a-bubble-layer"></a>Zobrazit popisky s bublinovou vrstvou

Tento kód ukazuje, jak použít bublinovou vrstvu pro vykreslení bodu na mapě. A jak použít vrstvu symbolů pro vykreslení popisku. Chcete-li skrýt ikonu vrstvy symbolů, nastavte `iconImage` možnost na `"none"` .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641,47.627631));

//Add a bubble layer.
map.layers.add(new BubbleLayer(source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
));

//Add a symbol layer to display text, hide the icon image.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),
    textField("Museum of History & Industry (MOHAI)"),
    textColor("#005995"),
    textOffset(new Float[]{0f, -2.2f})
));
```

Následující snímek obrazovky ukazuje, že výše uvedený kód rending bod v bublinové vrstvě a textový popisek pro bod s vrstvou symbolů.

![Mapovat s bodem vykresleným pomocí bublinové vrstvy a textového popisku s vrstvou symbolů](media/map-add-bubble-layer-android/android-bubble-symbol-layer.png)

## <a name="next-steps"></a>Další kroky

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Zobrazení informací o funkci](display-feature-information-android.md)
