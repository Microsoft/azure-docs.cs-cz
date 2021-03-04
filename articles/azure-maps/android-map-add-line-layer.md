---
title: Přidat řádkovou vrstvu do map pro Android | Mapy Microsoft Azure
description: Naučte se přidávat řádky do map. Podívejte se na příklady, které používají Android SDK Azure Maps k přidání vrstev čáry do map a k přizpůsobení čar se symboly a barevnými přechody.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 62002b776262e97dd34db1d9ecd3b7b0e09f46f3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044220"
---
# <a name="add-a-line-layer-to-the-map-android-sdk"></a>Přidat řádkovou vrstvu na mapu (Android SDK)

Spojnicová vrstva se dá použít k vykreslování `LineString` a zobrazování `MultiLineString` funkcí jako cest nebo tras na mapě. Řádkovou vrstvu lze také použít k vykreslení osnovy `Polygon` a `MultiPolygon` funkcí. Zdroj dat je připojen ke spojnici čáry, aby byl zajištěn pro vykreslování dat.

> [!TIP]
> Vrstvy čar ve výchozím nastavení vykreslí souřadnice mnohoúhelníků a také čáry ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykreslovat pouze funkce geometrie LineString, nastavte `filter` možnost vrstvy na `eq(geometryType(), "LineString")` . Pokud chcete zahrnout i funkce MultiLineString, nastavte `filter` možnost vrstvy na `any(eq(geometryType(), "LineString"), eq(geometryType(), "MultiLineString"))` .

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste dokončili kroky v [rychlém startu: vytvoření dokumentu aplikace pro Android](quick-android-map.md) . Bloky kódu v tomto článku lze vložit do `onReady` obslužné rutiny události Maps.

## <a name="add-a-line-layer"></a>Přidání řádkové vrstvy

Následující kód ukazuje, jak vytvořit řádek. Přidejte čáru ke zdroji dat a potom ji vykreslete pomocí vrstvy čáry pomocí `LineLayer` třídy.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a list of points.
List<Point> points = Arrays.asList(
    Point.fromLngLat(-73.972340, 40.743270),
    Point.fromLngLat(-74.004420, 40.756800));

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points));

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor("blue"),
    strokeWidth(5f)
);

map.layers.add(layer);
```

Následující snímek obrazovky ukazuje, že výše uvedený kód vykresluje čáru v čárové vrstvě.

![Mapování s čárou vykresleným pomocí vrstvy čáry](media/android-map-add-line-layer/android-line-layer.png)

## <a name="data-drive-line-style"></a>Styl čáry datové jednotky

Následující kód vytvoří dvě řádkové funkce a přidá hodnotu omezení rychlosti jako vlastnost na každý řádek. Spojnicová vrstva používá výraz stylu datové jednotky barva výrazu na základě hodnoty omezení rychlosti. Vzhledem k tomu, že se data čar překrývají podél cest, níže uvedený kód přidá do vrstvy popisku čáru čáry, aby bylo možné zřetelně číst popisky cest.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a line feature.
Feature feature = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.131821, 47.704033),
        Point.fromLngLat(-122.099919, 47.703678))));

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35);

//Add the feature to the data source.
source.add(feature);

//Create a second line feature.
Feature feature2 = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.126662, 47.708265),
        Point.fromLngLat(-122.126877, 47.703980))));

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15);

//Add the second feature to the data source.
source.add(feature2);

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
);

map.layers.add(layer, "labels");
```

Následující snímek obrazovky ukazuje, že výše uvedený kód vykresluje dva řádky vrstvy čáry s barvou, která je načítána z výrazu stylu řízeného daty na základě vlastnosti v rámci čáry.

![Mapování s použitím řádků se stylem datové jednotky vykreslenými v čárové vrstvě](media/android-map-add-line-layer/android-line-layer-data-drive-style.png)

## <a name="add-a-stroke-gradient-to-a-line"></a>Přidání přechodu mezi tahy na čáru

Pro čáru můžete použít jednu barvu tahu. Můžete také vyplnit řádek s přechodem barev pro zobrazení přechodu z jednoho segmentu čáry do dalšího segmentu čáry. Například barevné přechody lze použít k reprezentaci změn v průběhu času a vzdálenosti nebo různých teplot v rámci připojeného řádku objektů. Aby bylo možné tuto funkci použít na řádek, zdroj dat musí mít `lineMetrics` možnost nastavenou na hodnotu `true` a poté může být výraz barevného přechodu předána `strokeColor` Možnosti řádku. Výraz přechodu na tah musí odkazovat na `lineProgress` datový výraz, který zpřístupňuje metriku počítaného řádku k výrazu.

```java
//Create a data source and add it to the map.
source = new DataSource(
    //Enable line metrics on the data source. This is needed to enable support for strokeGradient.
    withLineMetrics(true)
);
map.sources.add(source);

//Create a line and add it to the data source.
source.add(LineString.fromLngLats(
    Arrays.asList(
        Point.fromLngLat(-122.18822, 47.63208),
        Point.fromLngLat(-122.18204, 47.63196),
        Point.fromLngLat(-122.17243, 47.62976),
        Point.fromLngLat(-122.16419, 47.63023),
        Point.fromLngLat(-122.15852, 47.62942),
        Point.fromLngLat(-122.15183, 47.62988),
        Point.fromLngLat(-122.14256, 47.63451),
        Point.fromLngLat(-122.13483, 47.64041),
        Point.fromLngLat(-122.13466, 47.64422),
        Point.fromLngLat(-122.13844, 47.65440),
        Point.fromLngLat(-122.13277, 47.66515),
        Point.fromLngLat(-122.12779, 47.66712),
        Point.fromLngLat(-122.11595, 47.66712),
        Point.fromLngLat(-122.11063, 47.66735),
        Point.fromLngLat(-122.10668, 47.67035),
        Point.fromLngLat(-122.10565, 47.67498)
    )
));

//Create a line layer and pass in a gradient expression for the strokeGradient property.
map.layers.add(new LineLayer(source,
    strokeWidth(6f),

    //Pass an interpolate or step expression that represents a gradient.
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
));
```

Následující snímek obrazovky ukazuje výše uvedený kód zobrazující čáru vykreslenou pomocí barvy tahu přechodu.

![Mapování pomocí čáry vykreslené jako cesty přechodu na řádkovou vrstvu](media/android-map-add-line-layer/android-line-layer-gradient.jpg)

## <a name="add-symbols-along-a-line"></a>Přidat symboly podél čáry

Tento příklad ukazuje, jak přidat ikony šipek podél čáry na mapě. Při použití vrstvy symbolů nastavte `symbolPlacement` možnost na `SymbolPlacement.LINE` . Tato možnost vykreslí symboly podél čáry a otočí ikony (0 stupňů = vpravo).

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple-arrow-right);

//Create and add a line to the data source.
source.add(LineString.fromLngLats(Arrays.asList(
    Point.fromLngLat(-122.18822, 47.63208),
    Point.fromLngLat(-122.18204, 47.63196),
    Point.fromLngLat(-122.17243, 47.62976),
    Point.fromLngLat(-122.16419, 47.63023),
    Point.fromLngLat(-122.15852, 47.62942),
    Point.fromLngLat(-122.15183, 47.62988),
    Point.fromLngLat(-122.14256, 47.63451),
    Point.fromLngLat(-122.13483, 47.64041),
    Point.fromLngLat(-122.13466, 47.64422),
    Point.fromLngLat(-122.13844, 47.65440),
    Point.fromLngLat(-122.13277, 47.66515),
    Point.fromLngLat(-122.12779, 47.66712),
    Point.fromLngLat(-122.11595, 47.66712),
    Point.fromLngLat(-122.11063, 47.66735),
    Point.fromLngLat(-122.10668, 47.67035),
    Point.fromLngLat(-122.10565, 47.67498)))
);

//Create a line layer and add it to the map.
map.layers.add(new LineLayer(source,
    strokeColor("DarkOrchid"),
    strokeWidth(5f)
));

//Create a symbol layer and add it to the map.
map.layers.add(new SymbolLayer(source,
    //Space symbols out along line.
    symbolPlacement(SymbolPlacement.LINE),

    //Spread the symbols out 100 pixels apart.
    symbolSpacing(100f),
    
    //Use the arrow icon as the symbol.
    iconImage("arrow-icon"),

    //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    iconAllowOverlap(true),

    //Center the symbol icon.
    iconAnchor(AnchorType.CENTER),

    //Scale the icon size.
    iconSize(0.8f)
));
```

V této ukázce byl do nakreslené složky aplikace načten následující obrázek.

| ![Obrázek ikony fialové šipky](media/android-map-add-line-layer/purple-arrow-right.png)|
|:-----------------------------------------------------------------------:|
|                                                  |

Níže uvedený snímek obrazovky ukazuje výše uvedený kód, který zobrazuje čáru s ikonami šipek zobrazenými podél sebe.

![Mapování s řádky se stylem data-jednotka se šipkami vykreslenými ve spojnicové vrstvě](media/android-map-add-line-layer/android-symbols-along-line-path.png)

## <a name="next-steps"></a>Další kroky

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](how-to-add-shapes-to-android-map.md)
