---
title: Přidat vrstvu symbolů do map pro Android | Mapy Microsoft Azure
description: Naučte se, jak přidat značku k mapě. Podívejte se na příklad, který používá Android SDK Azure Maps k přidání vrstvy symbolu, která obsahuje data založená na bodech ze zdroje dat.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 040fcde35707074ffaf102ed6c224b2f47a084bb
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679329"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Přidat vrstvu symbolu (Android SDK)

V tomto článku se dozvíte, jak vykreslovat data bodů ze zdroje dat jako vrstvu symbolů na mapě pomocí Android SDK Azure Maps. Vrstvy symbolů vykreslují body jako obrázek a text na mapě.

> [!TIP]
> Vrstvy symbolů ve výchozím nastavení vykreslí souřadnice všech geometrií ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykreslí pouze funkce geometrie bodu, nastavte `filter` možnost vrstvy na `eq(geometryType(), "Point")` . Pokud chcete zahrnout i funkce systému MultiPoint, nastavte `filter` možnost vrstvy na `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste dokončili kroky v [rychlém startu: vytvoření dokumentu aplikace pro Android](quick-android-map.md) . Bloky kódu v tomto článku lze vložit do `onReady` obslužné rutiny události Maps.

## <a name="add-a-symbol-layer"></a>Přidání vrstvy symbolů

Předtím, než můžete na mapu přidat vrstvu symbolů, je nutné provést několik kroků. Nejprve vytvořte zdroj dat a přidejte jej do mapy. Vytvoří symbolovou vrstvu. Potom předejte zdroj dat do vrstvy symbolů, aby bylo možné načíst data ze zdroje dat. Nakonec přidejte data do zdroje dat, aby bylo vygenerováno něco.

Následující kód ukazuje, co by mělo být přidáno do mapy poté, co bylo načteno. Tato ukázka vykreslí jeden bod na mapě pomocí vrstvy symbolů.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

Existují tři různé typy datových bodů, které lze přidat do mapy:

- Geometrická geometrie bodu JSON – tento objekt obsahuje pouze souřadnici bodu a nic jiného. `Point.fromLngLat`Statickou metodu lze použít k snadnému vytváření těchto objektů.
- Geometrická geometrie systému pro data JSON – tento objekt obsahuje souřadnice více bodů a nic jiného. Předejte pole bodů do `MultiPoint` třídy pro vytvoření těchto objektů.
- Geografická funkce JSON – tento objekt se skládá z libovolného geometrického geometrie a sady vlastností, které obsahují metadata přidružená k geometrii.

Další informace najdete v dokumentu [Vytvoření zdroje dat](create-data-source-android-sdk.md) při vytváření a přidávání dat na mapu.

Následující ukázka kódu vytvoří geometrii geometrického bodu JSON a předá ji do funkce "geometrické JSON" a `title` přidá hodnotu do jejích vlastností. `title`Vlastnost se zobrazuje jako text nad ikonou symbolu na mapě.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

Následující snímek obrazovky ukazuje, že výše uvedený kód rending funkci Point pomocí ikony a textového popisku s vrstvou symbolů.

![Mapovat s bodem vykresleným pomocí vrstvy symbolů zobrazující ikonu a textový popisek pro funkci bodu](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Ve výchozím nastavení vrstvy symbolů optimalizují vykreslování symbolů skrytím symbolů, které se překrývají. Při přiblížení se budou skryté symboly zobrazovat. Chcete-li tuto funkci zakázat a vykreslit všechny symboly za všech okolností, nastavte `iconAllowOverlap` Možnosti a na `textAllowOverlap` `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Přidání vlastní ikony do vrstvy symbolů

Vrstvy symbolů se vykreslují pomocí WebGL. Jako takové všechny prostředky, například obrázky ikon, je nutné načíst do kontextu WebGL. Tento příklad ukazuje, jak přidat vlastní ikonu k prostředkům mapy. Tato ikona se pak použije k vykreslení dat bodů s vlastním symbolem na mapě. `textField`Vlastnost vrstvy symbolů vyžaduje, aby byl zadán výraz. V tomto případě chceme vykreslit vlastnost teploty. Vzhledem k tomu, že teplota je číslo, je nutné ji převést na řetězec. Navíc chceme k němu připojit "°F". Výraz lze použít k provedení tohoto zřetězení; `concat(Expression.toString(get("temperature")), literal("°F"))`.

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

V této ukázce byl do nakreslené složky aplikace načten následující obrázek.

| ![Ikona počasí obrázek sprchových sprch](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

Následující snímek obrazovky ukazuje, že výše uvedený kód rending funkce Point pomocí vlastní ikony a formátovaného textového popisku pomocí vrstvy symbolů.

![Mapovat s bodem vykresleným pomocí vrstvy symbolu, která zobrazuje vlastní ikonu a formátovaný text popisku pro funkci bodu](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Pokud chcete vykreslit pouze text s vrstvou symbolů, můžete ikonu skrýt nastavením `iconImage` Vlastnosti možnosti ikony na `"none"` .

## <a name="next-steps"></a>Další kroky

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Zobrazení informací o funkci](display-feature-information-android.md)
