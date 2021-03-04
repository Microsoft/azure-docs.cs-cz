---
title: Přidat mnohoúhelníkovou vrstvu do map pro Android | Mapy Microsoft Azure
description: Naučte se přidávat mnohoúhelníky nebo kružnice do map. Podívejte se, jak používat Android SDK Azure Maps k přizpůsobení geometrických tvarů a usnadnění jejich aktualizace a údržby.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 25785ae7a214d6122fb90b80e8f0725a3468c48d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047590"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>Přidat mnohoúhelníkovou vrstvu na mapu (Android SDK)

V tomto článku se dozvíte, jak vykreslovat oblasti `Polygon` a `MultiPolygon` funkce geometrií na mapě pomocí mnohoúhelníkové vrstvy.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste dokončili kroky v [rychlém startu: vytvoření dokumentu aplikace pro Android](quick-android-map.md) . Bloky kódu v tomto článku lze vložit do `onReady` obslužné rutiny události Maps.

## <a name="use-a-polygon-layer"></a>Použít mnohoúhelníkovou vrstvu

Když je mnohoúhelníková vrstva připojená ke zdroji dat a načte se na mapě, vykreslí oblast s `Polygon` funkcemi a `MultiPolygon` . Chcete-li vytvořit mnohoúhelník, přidejte ho do zdroje dat a vykreslete ho pomocí mnohoúhelníkové vrstvy pomocí `PolygonLayer` třídy.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

Následující snímek obrazovky ukazuje, že výše uvedený kód vykresluje oblast mnohoúhelníku pomocí mnohoúhelníkové vrstvy.

![Mnohoúhelník se vykreslenou oblastí výplně](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>Použít mnohoúhelník a čáru vrstev společně

Spojnicová vrstva se používá k vykreslení obrysu mnohoúhelníků. Následující ukázka kódu vykreslí mnohoúhelník jako předchozí příklad, ale nyní přidá řádkovou vrstvu. Tato vrstva čáry je druhá vrstva připojená ke zdroji dat.  

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

Následující snímek obrazovky ukazuje, že výše uvedený kód vykresluje mnohoúhelník s jeho obrysem vykresleným pomocí spojnicové vrstvy.

![Mnohoúhelník s plochou výplně a vykresleným osnovou](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> Při sbalení mnohoúhelníku s vrstvou čáry Nezapomeňte uzavřít všechny prstence v mnohoúhelníkech tak, aby každé pole bodů bylo stejného počátečního a koncového bodu. Pokud tato funkce není hotova, čára čáry pravděpodobně nepřipojí poslední bod mnohoúhelníku k prvnímu bodu.

## <a name="fill-a-polygon-with-a-pattern"></a>Naplnění mnohoúhelníku vzorem

Kromě naplnění mnohoúhelníku barvou můžete mnohoúhelník vyplnit pomocí vzorku obrázku. Načtěte vzorek obrázku do prostředků mapy spritch obrázků a pak na tento obrázek odkázat pomocí `fillPattern` Možnosti mnohoúhelníkové vrstvy.

```java
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-50, -20),
            Point.fromLngLat(0, 40),
            Point.fromLngLat(50, -20),
            Point.fromLngLat(-50, -20)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
), "labels");
```

V této ukázce byl do nakreslené složky aplikace načten následující obrázek.

| ![Obrázek ikony fialové šipky](media/how-to-add-shapes-to-android-map/fill-checker-red.png)|
|:-----------------------------------------------------------------------:|
| fill_checker_red.png                                                    |

Níže je snímek obrazovky výše uvedeného kódu, který vykresluje mnohoúhelník se vzorkem výplně na mapě.

![Mnohoúhelník se vzorkem výplně vykresleným na mapě](media/how-to-add-shapes-to-android-map/android-polygon-pattern.jpg)

## <a name="next-steps"></a>Další kroky

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání vysunuté mnohoúhelníkové vrstvy](map-extruded-polygon-android.md)
