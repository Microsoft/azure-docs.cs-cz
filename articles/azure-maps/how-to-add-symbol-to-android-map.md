---
title: Přidat vrstvu symbolů do map pro Android | Mapy Microsoft Azure
description: Naučte se, jak přidat značku k mapě. Podívejte se na příklad, který používá Android SDK Azure Maps k přidání vrstvy symbolu, která obsahuje data založená na bodech ze zdroje dat.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: edb758469a06dcb7914025ea449b9d952e939533
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097206"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Přidat vrstvu symbolu (Android SDK)

V tomto článku se dozvíte, jak vykreslovat data bodů ze zdroje dat jako vrstvu symbolů na mapě pomocí Android SDK Azure Maps. Vrstvy symbolů vykreslují body jako obrázek a text na mapě.

> [!TIP]
> Vrstvy symbolů ve výchozím nastavení vykreslí souřadnice všech geometrií ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykreslí pouze funkce geometrie bodu, nastavte `filter` možnost vrstvy na `eq(geometryType(), "Point")` . Pokud chcete zahrnout i funkce systému MultiPoint, nastavte `filter` možnost vrstvy na `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Předpoklady

Ujistěte se, že jste dokončili kroky v [rychlém startu: vytvoření dokumentu aplikace pro Android](quick-android-map.md) . Bloky kódu v tomto článku lze vložit do `onReady` obslužné rutiny události Maps.

## <a name="add-a-symbol-layer"></a>Přidání vrstvy symbolů

Předtím, než můžete na mapu přidat vrstvu symbolů, je nutné provést několik kroků. Nejprve vytvořte zdroj dat a přidejte jej do mapy. Vytvoří symbolovou vrstvu. Potom předejte zdroj dat do vrstvy symbolů, aby bylo možné načíst data ze zdroje dat. Nakonec přidejte data do zdroje dat, aby bylo vygenerováno něco.

Následující kód ukazuje, co by mělo být přidáno do mapy poté, co bylo načteno. Tato ukázka vykreslí jeden bod na mapě pomocí vrstvy symbolů.

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(source)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

Existují tři různé typy datových bodů, které lze přidat do mapy:

- Geometrická geometrie bodu JSON – tento objekt obsahuje pouze souřadnici bodu a nic jiného. `Point.fromLngLat`Statickou metodu lze použít k snadnému vytváření těchto objektů.
- Geometrická geometrie systému pro data JSON – tento objekt obsahuje souřadnice více bodů a nic jiného. Předejte pole bodů do `MultiPoint` třídy pro vytvoření těchto objektů.
- Geografická funkce JSON – tento objekt se skládá z libovolného geometrického geometrie a sady vlastností, které obsahují metadata přidružená k geometrii.

Další informace najdete v dokumentu [Vytvoření zdroje dat](create-data-source-android-sdk.md) při vytváření a přidávání dat na mapu.

Následující ukázka kódu vytvoří geometrii geometrického bodu JSON a předá ji do funkce "geometrické JSON" a `title` přidá hodnotu do jejích vlastností. `title`Vlastnost se zobrazuje jako text nad ikonou symbolu na mapě.

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(0, 0))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,  //Get the title property of the feature and display it on the map.
    textField(get("title"))
)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

Následující snímek obrazovky ukazuje, že výše uvedený kód rending funkci Point pomocí ikony a textového popisku s vrstvou symbolů.

![Mapovat s bodem vykresleným pomocí vrstvy symbolů zobrazující ikonu a textový popisek pro funkci bodu](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Ve výchozím nastavení vrstvy symbolů optimalizují vykreslování symbolů skrytím symbolů, které se překrývají. Při přiblížení se budou skryté symboly zobrazovat. Chcete-li tuto funkci zakázat a vykreslit všechny symboly za všech okolností, nastavte `iconAllowOverlap` Možnosti a na `textAllowOverlap` `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Přidání vlastní ikony do vrstvy symbolů

Vrstvy symbolů se vykreslují pomocí WebGL. Jako takové všechny prostředky, například obrázky ikon, je nutné načíst do kontextu WebGL. Tento příklad ukazuje, jak přidat vlastní ikonu k prostředkům mapy. Tato ikona se pak použije k vykreslení dat bodů s vlastním symbolem na mapě. `textField`Vlastnost vrstvy symbolů vyžaduje, aby byl zadán výraz. V tomto případě chceme vykreslit vlastnost teploty. Vzhledem k tomu, že teplota je číslo, je nutné ji převést na řetězec. Navíc chceme k němu připojit "°F". Výraz lze použít k provedení tohoto zřetězení; `concat(Expression.toString(get("temperature")), literal("°F"))`.

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773))

//Add a property to the feature.
feature.addNumberProperty("temperature", 64)

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),  //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(arrayOf(0f, -1.5f))
)
```

::: zone-end

V této ukázce byl do nakreslené složky aplikace načten následující obrázek.

| ![Ikona počasí obrázek sprchových sprch](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

Následující snímek obrazovky ukazuje, že výše uvedený kód rending funkce Point pomocí vlastní ikony a formátovaného textového popisku pomocí vrstvy symbolů.

![Mapovat s bodem vykresleným pomocí vrstvy symbolu, která zobrazuje vlastní ikonu a formátovaný text popisku pro funkci bodu](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Pokud chcete vykreslit pouze text s vrstvou symbolů, můžete ikonu skrýt nastavením `iconImage` Vlastnosti možnosti ikony na `"none"` .

## <a name="modify-symbol-colors"></a>Upravit barvy symbolů

Azure Maps Android SDK obsahuje sadu předdefinovaných variací barev výchozí ikony značky. Například `marker-red` může být předán do `iconImage` Možnosti vrstvy symbolů pro vykreslení červené verze ikony značky v této vrstvě.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    iconImage("marker-red")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    iconImage("marker-red")
)
```

::: zone-end

V tabulce níže jsou uvedeny všechny dostupné názvy obrázků ikon. Všechny tyto značky vyžádají své barvy z barevných prostředků, které lze přepsat. Kromě přepsání hlavní barvy výplně této značky. Všimněte si však, že přepsání barvy jedné z těchto značek bude platit pro všechny vrstvy, které používají tuto ikonu obrázku.

| Název obrázku ikony | Název prostředku barvy |
|-----------------|---------------------|
| `marker-default` | `mapcontrol_marker_default` |
| `marker-black` | `mapcontrol_marker_black` |
| `marker-blue` | `mapcontrol_marker_blue` |
| `marker-darkblue` | `mapcontrol_marker_darkblue` |
| `marker-red` | `mapcontrol_marker_red` |
| `marker-yellow` | `mapcontrol_marker_yellow` |

Můžete také přepsat barvu ohraničení všech značek pomocí `mapcontrol_marker_border` názvu barevného prostředku. Barvy těchto značek lze přepsat přidáním barvy se stejným názvem do `colors.xml` souboru aplikace. Například následující `colors.xml` soubor nastaví barvu výchozí značky jasně zelenou.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="mapcontrol_marker_default">#00FF00</color>
</resources>
```

Následující je upravená verze výchozího vektoru XML značky, kterou můžete upravit a vytvořit tak další vlastní verze výchozí značky. Upravenou verzi lze přidat do `drawable` složky aplikace a přidat ji do Sprite obrázku mapy pomocí `map.images.add` a potom použít s vrstvou symbolů.

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24.5dp"
    android:height="36.5dp"
    android:viewportWidth="24.5"
    android:viewportHeight="36.5">
    <path
        android:pathData="M12.25,0.25a12.2543,12.2543 0,0 0,-12 12.4937c0,6.4436 6.4879,12.1093 11.059,22.5641 0.5493,1.2563 1.3327,1.2563 1.882,0C17.7621,24.8529 24.25,19.1857 24.25,12.7437A12.2543,12.2543 0,0 0,12.25 0.25Z"
        android:strokeWidth="0.5"
        android:fillColor="@color/mapcontrol_marker_default"
        android:strokeColor="@color/mapcontrol_marker_border"/>
</vector>
```

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
