---
title: Zobrazit informace o funkcích v nástrojích pro mapování Androidu | Mapy Microsoft Azure
description: Naučte se zobrazovat informace, když uživatelé komunikují s funkcemi mapy. Pomocí Android SDK Azure Maps můžete zobrazit informační zprávy a jiné typy zpráv.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ea9ed2c74651a7719533340a24c4741d4e25b805
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605718"
---
# <a name="display-feature-information"></a>Zobrazení informací o funkci

Prostorová data jsou často reprezentovaná pomocí bodů, čar a mnohoúhelníků. Tato data často obsahují informace o metadatech, které jsou k ní přidružené. Například bod může představovat umístění restaurace a metadata o této restauraci může být jeho jméno, adresa a typ potravin, který IT oddělení obsluhuje. Tato metadata lze přidat jako vlastnosti pro vlastnost injson `Feature` . Následující kód vytvoří jednoduchou funkci Point s `title` vlastností, která má hodnotu "Hello World!".

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature)
```

::: zone-end

Způsoby, jak vytvořit a přidat data na mapu, najdete v dokumentaci k [Vytvoření zdroje dat](create-data-source-android-sdk.md) .

Když uživatel komunikuje s funkcí na mapě, můžou se události použít k reakci na tyto akce. Běžným scénářem je zobrazení zprávy z vlastností metadat funkce, se kterou uživatel pracuje. `OnFeatureClick`Událost je hlavní událost, která se používá k detekci, kdy uživatel klepne na funkci na mapě. K dispozici je také `OnLongFeatureClick` událost. Při přidávání `OnFeatureClick` události do mapy může být omezena na jednu vrstvu předáním ID vrstvy, na kterou se omezuje. Pokud není předáno žádné ID vrstvy, klepněte na mapě na libovolné funkci, bez ohledu na to, ve které vrstvě se nachází, by tato událost mohla vyvolat. Následující kód vytvoří vrstvu symbolů pro vykreslení dat bodů na mapě, poté přidá `OnFeatureClick` událost a omezí ji na tuto vrstvu symbolů.

::: zone pivot="programming-language-java-android"

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a symbol and add it to the map.
val layer = SymbolLayer(source)
map.layers.add(layer)

//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Do something with the message.

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

## <a name="display-a-toast-message"></a>Zobrazit informační zprávy

Zpráva informační zprávy je jedním z nejjednodušších způsobů zobrazení informací uživateli a je k dispozici ve všech verzích Androidu. Nepodporuje žádný typ vstupu uživatele a zobrazuje se jenom po krátkou dobu. Pokud chcete uživateli rychle informovat o tom, co se na něj klepnuli, může být informační zpráva vhodná. Následující kód ukazuje, jak lze v události použít informační zprávy `OnFeatureClick` .

::: zone pivot="programming-language-java-android"

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show()

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

![Animace používané funkce a zobrazená zpráva informační zprávy](media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Kromě informačních zpráv existuje mnoho dalších způsobů, jak prezentovat vlastnosti metadat funkce, jako například:

- [Widget snackbar](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` Poskytněte nezjednodušenou zpětnou vazbu k operaci. Zobrazují v dolní části obrazovky na mobilním zařízení stručnou zprávu a na větších zařízeních zbývá dole. `Snackbars` zobrazí se nad všemi ostatními prvky na obrazovce a v jednu chvíli může být zobrazená jenom jedna.
- [Dialogová](https://developer.android.com/guide/topics/ui/dialogs) okna – dialogové okno je malé okno s výzvou, aby uživatel zadal rozhodnutí nebo zavedl Další informace. Dialogové okno neplní obrazovku a obvykle se používá pro modální události, které vyžadují, aby uživatelé před pokračováním prohlédli akci.
- Přidá [fragment](https://developer.android.com/guide/components/fragments) do aktuální aktivity.
- Přejděte k jiné aktivitě nebo zobrazení.

## <a name="display-a-popup"></a>Zobrazit místní nabídku

Azure Maps Android SDK poskytuje `Popup` třídu, která usnadňuje vytváření prvků poznámek uživatelského rozhraní, které jsou ukotveny do pozice na mapě. Pro automaticky otevíraná okna musíte předat zobrazení s relativním rozložením do `content` Možnosti automaticky otevírané okno. Tady je jednoduchý příklad rozložení, který zobrazuje tmavý text nad chvíli na pozadí.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

Za předpokladu, že výše uvedené rozložení je uloženo v souboru s názvem `popup_text.xml` ve `res -> layout` složce aplikace, následující kód vytvoří automaticky otevírané okno, přidá ho do mapy. Při kliknutí na funkci `title` se tato vlastnost zobrazí v `popup_text.xml` rozložení s použitím dolního středu rozložení, které je ukotveno na určenou pozici na mapě.

::: zone pivot="programming-language-java-android"

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Access the text view within the custom view and set the text to the title property of the feature.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText(props.get("title").getAsString());

    //Get the coordinates from the clicked feature and create a position object.
    List<Double> c = ((Point)(f.geometry())).coordinates();
    Position pos = new Position(c.get(0), c.get(1));

    //Set the options on the popup.
    popup.setOptions(
        //Set the popups position.
        position(pos),

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),

        //Set the content of the popup.
        content(customView)

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    );

    //Open the popup.
    popup.open();

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)

map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Access the text view within the custom view and set the text to the title property of the feature.
    val tv: TextView = customView.findViewById(R.id.message)
    tv.text = props!!["title"].asString

    //Get the coordinates from the clicked feature and create a position object.
    val c: List<Double> = (f.geometry() as Point?).coordinates()
    val pos = Position(c[0], c[1])

    //Set the options on the popup.
    popup.setOptions( 
        //Set the popups position.
        position(pos),  

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  

        //Set the content of the popup.
        content(customView) 

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    )

    //Open the popup.
    popup.open()

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
})
```

::: zone-end

Následující snímek obrazovky ukazuje automaticky otevíraná okna, která se zobrazují při kliknutí na funkce a při přesunu do jejich zadaného umístění na mapě.

![Animace zobrazeného překryvného okna a mapa přesunutá pomocí překryvného okna na pozici na mapě](./media/display-feature-information-android/android-popup.gif)

## <a name="next-steps"></a>Další kroky

Přidání dalších dat do mapy:

> [!div class="nextstepaction"]
> [Reagovat na události mapování](android-map-events.md)

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](how-to-add-shapes-to-android-map.md)
