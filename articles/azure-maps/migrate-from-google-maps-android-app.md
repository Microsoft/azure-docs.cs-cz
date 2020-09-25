---
title: Migrace aplikace pro Android | Mapy Microsoft Azure
description: Postup migrace aplikace pro Android z Google Maps na mapy Microsoft Azure
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 96f68bdf58d9cc98815e4ae76ae01da87c38dc88
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319635"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migrace aplikace pro Android z Google Maps

Android SDK Azure Maps má rozhraní API, které je podobné webové sadě SDK. Pokud jste se seznámili s jednou z těchto sad SDK, platí mnoho ze stejných konceptů, osvědčených postupů a architektur.

Android SDK Azure Maps podporuje minimální verzi Androidu API 21: Android 5.0.0 (Lupa).

Všechny příklady jsou k dispozici v jazyce Java; Kotlin ale můžete použít s Android SDK Azure Maps.

Další informace o vývoji Android SDK pomocí Azure Maps naleznete v tématu [návody pro Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="load-a-map"></a>Načtení mapy

Načtení mapy v aplikaci pro Android pomocí Google nebo Azure Maps se skládá z podobných kroků. Při použití sady SDK musíte:

* Získejte rozhraní API nebo klíč předplatného pro přístup k libovolné platformě.
* Přidejte do aktivity nějaký kód XML k určení, kde má být mapa vykreslena a jak má být rozložena.
* Přepište všechny metody životního cyklu z aktivity obsahující zobrazení mapy na odpovídající metody v mapě třídy. Konkrétně je nutné přepsat následující metody:
    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`
* Než se pokusíte o přístup a Programujte, počkejte, než bude mapa připravena.

### <a name="before-google-maps"></a>Před: Google Maps

Pokud chcete zobrazit mapu pomocí Google Maps SDK pro Android, provede se následující postup:

1. Ujistěte se, že jsou nainstalované služby Google Play.
2. Přidejte závislost pro službu Google Maps do souboru  **Gradle. Build** modulu:

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

3. Přidejte klíč rozhraní API pro Google Maps do oddílu aplikace v souboru  ** \_ \_api.xmlmapy Google ** :

    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

4. Přidejte do hlavní aktivity fragment mapy:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

5. V souboru **MainActivity. Java** budete muset importovat sadu Google Maps SDK. Předejte všechny metody životního cyklu z aktivity, která obsahuje zobrazení mapy, do odpovídajících těch v rámci třídy map. Načtěte `MapView` instanci z fragmentu mapy pomocí `getMapAsync(OnMapReadyCallback)` metody. `MapView`Automaticky inicializuje systém map a zobrazení. Upravte soubor **MainActivity. Java** následujícím způsobem:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Při spuštění aplikace se mapový ovládací prvek načte jako na následujícím obrázku.

![Jednoduchá mapování Google](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Chcete-li zobrazit mapu pomocí sady Azure Maps SDK pro Android, je nutné provést následující kroky:

1. Otevřete soubor **Build. Gradle** nejvyšší úrovně a přidejte následující kód do části bloku **všech projektů** :

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualizujte svou **aplikaci/Build. Gradle** a přidejte do ní následující kód:

    1. Ujistěte se, že je **hodnotu minsdkversion** projektu na rozhraní API 21 nebo vyšší.

    2. Do části Android přidejte následující kód:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Aktualizujte svůj blok závislostí. Přidat novou řadu implementačních závislostí pro nejnovější Azure Maps Android SDK:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Android SDK Azure Maps se pravidelně upgradují a zvyšují. Nejnovější verzi Azure Maps získáte pomocí [ovládacího prvku Začínáme s nástrojem pro správu Androidu](how-to-use-android-map-control-library.md) . Můžete také nastavit číslo verze z "0,2" na "0 +", aby váš kód vždy odkazoval na nejnovější verzi.

    4. Přejděte na **soubor** na panelu nástrojů a pak klikněte na **synchronizovat projekt se soubory Gradle**.

3. Přidání fragmentu mapy k hlavní aktivitě (prostředky \>main.xml aktivity rozložení pro zdroj \> \_ ):

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. V souboru **MainActivity. Java** budete potřebovat:

    * Importuje sadu Azure Maps SDK.
    * Nastavení ověřovacích informací Azure Maps
    * Získání instance mapového ovládacího prvku v metodě **Create**

     Nastavte ověřovací informace ve `AzureMaps` třídě pomocí `setSubscriptionKey` `setAadProperties` metod nebo. Tato globální aktualizace zajistí, že přidáte informace o ověřování do každého zobrazení.

    Mapový ovládací prvek obsahuje vlastní metody životního cyklu pro správu životního cyklu OpenGL pro Android. Tyto metody musí být volány přímo z obsažené aktivity. Pro správné volání metod životního cyklu ovládacího prvku mapy je nutné přepsat následující metody životního cyklu v aktivitě, která obsahuje mapový ovládací prvek. Zavolejte odpovídající metodu mapového ovládacího prvku.

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Upravte soubor **MainActivity. Java** následujícím způsobem:

    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
     
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

Pokud spustíte aplikaci, mapový ovládací prvek se načte jako na následujícím obrázku.


![Jednoduchá Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)

Všimněte si, že Azure Maps ovládací prvek podporuje další přiblížení a poskytuje více zobrazení světa.

> [!TIP]
> Pokud používáte emulátor Androidu na počítači s Windows, mapa se nemusí vykreslit kvůli konfliktům s OpenGL a softwarovým vykreslováním s akcelerovanými grafickými nástroji. Následující byly pro některé lidi pracovaly k vyřešení tohoto problému. Otevřete Správce AVD a vyberte virtuální zařízení, které chcete upravit. Přejděte dolů na panelu **Konfigurace ověření** . V části **Emulovaný výkon** nastavte možnost **Grafika** na **hardware**.

## <a name="localizing-the-map"></a>Lokalizace mapy

Lokalizace je důležitá, pokud je vaše cílová skupina rozdělená do několika zemí nebo oblastí nebo hovoří s různými jazyky.

### <a name="before-google-maps"></a>Před: Google Maps

Přidejte následující kód do `onCreate` metody pro nastavení jazyka mapy. Kód musí být přidán před nastavením kontextu zobrazení mapy. Kód jazyka "fr" omezuje jazyk na francouzštinu.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Tady je příklad Google Maps s jazykem nastaveným na "fr".

![Lokalizace Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Azure Maps poskytuje tři různé způsoby, jak nastavit jazyk a místní zobrazení mapy. První možností je předat jazyk a regionální informace o zobrazení do `AzureMaps` třídy. Tato možnost používá globálně statické `setLanguage` `setView` metody a. To znamená, že výchozí jazyk a místní zobrazení jsou nastaveny v rámci všech Azure Mapsch ovládacích prvků načtených ve vaší aplikaci. Tento příklad nastaví francouzštinu pomocí kódu jazyka "fr-FR".

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

Druhou možností je předat jazyk a zobrazit informace kódu XML mapového ovládacího prvku.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Třetí možností je naprogramovat jazyk a oblastní zobrazení mapy pomocí `setStyle` metody map. Tato možnost aktualizuje jazyk a regionální zobrazení, kdykoli se kód spustí.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Tady je příklad Azure Maps s jazykem nastaveným na "fr-FR".

![Azure Maps lokalizace](media/migrate-google-maps-android-app/azure-maps-localization.png)

Projděte si úplný seznam [podporovaných jazyků](supported-languages.md).

## <a name="setting-the-map-view"></a>Nastavení zobrazení mapy

Dynamická mapování v Azure Maps i Google Maps lze programově přesunout do nových geografických umístění voláním vhodných metod. Pojďme to udělat, aby se mapa zobrazovala v satelitním obrazovém programu, vycentrovat mapu do umístění s souřadnicemi a změnit úroveň přiblížení. V tomto příkladu použijeme zeměpisnou šířku: 35,0272, zeměpisná délka:-111,0225 a úroveň přiblížení 15.

### <a name="before-google-maps"></a>Před: Google Maps

Kameru ovládacího prvku mapy Google Maps se dá programově přesunout pomocí `moveCamera` metody. `moveCamera`Metoda umožňuje zadat střed mapy a úroveň přiblížení. `setMapType`Metoda změní typ mapy, která se má zobrazit.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

![Zobrazení sady Map Google](media/migrate-google-maps-android-app/google-maps-set-view.png)

> [!NOTE]
> Google Maps používá v dimenzích dlaždice, které jsou 256 pixelů, zatímco Azure Maps používá větší dlaždici 512 pixelů. Tím se sníží počet síťových požadavků, které Azure Maps k načtení stejné oblasti rozvržení jako Google Maps. Chcete-li dosáhnout stejné zobrazitelné oblasti jako mapy v Google Maps, je třeba při použití Azure Maps odečíst úroveň přiblížení v Google Maps.

### <a name="after-azure-maps"></a>Po: Azure Maps

Jak bylo uvedeno dříve, aby se dosáhlo stejné zobrazitelné oblasti v Azure Maps odečte úroveň přiblížení, která se používá v Google Maps po jednom. V takovém případě použijte úroveň přiblížení 14.

Počáteční zobrazení mapy lze nastavit v atributech XML na mapovém ovládacím prvku.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

Zobrazení mapy lze programovat pomocí map `setCamera` a `setStyle` metod.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

![Zobrazení sady Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**Další zdroje informací:**

* [Podporované styly map](supported-map-styles.md)

## <a name="adding-a-marker"></a>Přidání značky

Data bodu se často vykreslují pomocí obrázku na mapě. Tyto obrázky se označují jako značky, špendlíky, kolíky nebo symboly. Následující příklady vykreslí data bodu jako značky na mapě v šířce: 51,5, zeměpisná délka:-0,2.

### <a name="before-google-maps"></a>Před: Google Maps

Pomocí Map Google se značky přidávají pomocí metody map `addMarker` .

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

![Značka Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

V Azure Maps data bodu vykreslování na mapě nejprve přidejte data do zdroje dat. Pak propojíte tento zdroj dat se symbolovou vrstvou. Zdroj dat optimalizuje správu prostorových dat v mapovém ovládacím prvku. Vrstva symbolu určuje, jak se mají vykreslovat data bodů pomocí jako obrázek nebo text.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

![Značka Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>Přidání vlastní značky

Vlastní image lze použít k reprezentaci bodů na mapě. Mapa v níže uvedených příkladech používá vlastní obrázek k zobrazení bodu na mapě. Bod je zeměpisná šířka: 51,5 a zeměpisná délka:-0,2. Kotva Posune pozici značky, aby bod ikony připínáčku byl zarovnán se správnou polohou na mapě.

<center>

![Obrázek žlutého připínáčku](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>

V obou příkladech se výše uvedený obrázek přidá do vykreslené složky prostředků aplikací.

### <a name="before-google-maps"></a>Před: Google Maps

Pomocí Map Google lze vlastní image použít pro značky. Načtěte vlastní image pomocí možnosti značky `icon` . Chcete-li zarovnat bod obrázku na souřadnici, použijte `anchor` možnost. Kotva je relativní vzhledem k rozměrům obrázku. V tomto případě je kotva 0,2 jednotek na šířku a 1 jednotka je vysoká.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

![Vlastní značka Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Vrstvy symbolů v Azure Maps podporují vlastní image, ale nejdřív je potřeba načíst image do prostředků mapy a přiřadit jim jedinečné ID. Pak musí vrstva symbolu odkazovat na toto ID. Posunete symbol tak, aby se zarovnal ke správnému bodu na obrázku pomocí `iconOffset` Možnosti. Posun ikony je v pixelech. Ve výchozím nastavení je posun relativní vzhledem k dolnímu středu obrázku, ale tato hodnota posunu se dá upravit pomocí `iconAnchor` Možnosti. Tento příklad nastaví `iconAnchor` možnost na `"center"` . Používá posun ikony k přesunutí obrázku o 5 pixelů vpravo a 15 pixelů až do zarovnání s bodem odšpendlíku.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

![Azure Maps vlastní značka](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>Přidání lomené čáry

Lomené čáry slouží k reprezentaci čáry nebo cesty na mapě. Následující příklady ukazují, jak vytvořit přerušovanou lomenou čáru na mapě.

### <a name="before-google-maps"></a>Před: Google Maps

Pomocí mapy Google vykreslíte lomenou čáru pomocí `PolylineOptions` třídy. Přidejte lomenou čáru k mapě pomocí `addPolyline` metody. Nastavte barvu tahu pomocí `color` Možnosti. Šířku tahu nastavte pomocí `width` Možnosti. Pomocí možnosti přidejte pole pomlčky na tahy `pattern` .

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

![Lomená čáry Google Maps](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

V Azure Maps se označují jako `LineString` `MultiLineString` objekty. Přidejte tyto objekty do zdroje dat a vykreslete je pomocí spojnicové vrstvy. Šířku tahu nastavte pomocí `strokeWidth` Možnosti. Pomocí možnosti přidejte pole pomlčky na tahy `strokeDashArray` .

Jednotky pro tah a Pomlčkové pole "pixel" v sadě Azure Maps Web SDK jsou stejné jako ve službě Google Maps. Oba přijímají stejné hodnoty, aby se vytvořily stejné výsledky.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

![Azure Maps lomená čáry](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>Přidání mnohoúhelníku

Mnohoúhelníky slouží k reprezentaci oblasti na mapě. Následující příklady ukazují, jak vytvořit mnohoúhelník. Tento mnohoúhelník tvoří trojúhelník založený na souřadnici středu mapy.

### <a name="before-google-maps"></a>Před: Google Maps

Pomocí mapy Google vykreslí mnohoúhelník pomocí `PolygonOptions` třídy. Přidejte mnohoúhelník k mapě pomocí `addPolygon` metody. Nastavte barvy výplně a tahu pomocí `fillColor` možností a v `strokeColor` uvedeném pořadí. Šířku tahu nastavte pomocí `strokeWidth` Možnosti.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

![Mnohoúhelník mapy Google](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

V Azure Maps přidejte `Polygon` objekty a `MultiPolygon` objekty do zdroje dat a vykreslete je na mapě pomocí vrstev. Vykreslí oblast mnohoúhelníku ve vrstvě mnohoúhelníku. Vykreslí obrys mnohoúhelníku pomocí čárové vrstvy. Nastavte barvu a šířku tahu pomocí `strokeColor` `strokeWidth` možností a.

Jednotky šířky a přerušovaného pole "pixel" v sadě Azure Maps Web SDK se zarovnají s příslušnými jednotkami v Mapách Google Maps. Oba akceptují stejné hodnoty a vydávají stejné výsledky.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```
![Azure Maps mnohoúhelník](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>Překrytí vrstvy dlaždice

 K překrytí imagí vrstev, které byly rozděleny do menších dlaždic, které se zarovnají se systémem mapy dlážděn, použijte vrstvy dlaždic. Tento přístup je běžným způsobem překrývání imagí vrstev nebo velkých datových sad. Vrstvy dlaždic se označují jako překryvy obrázků v Google Maps.

V následujících příkladech je překrytá vrstva s paprskovou dlaždicí počasí z Iowaho prostředí Mesonet of Iowa State University. Velikost dlaždic je 256 pixelů.

### <a name="before-google-maps"></a>Před: Google Maps

Pomocí služby Google Maps je možné překrytí vrstvy dlaždic nad mapu. Použijte `TileOverlayOptions` třídu. Přidejte vrstvu dlaždice na mapu pomocí `addTileLauer` metody. Chcete-li, aby dlaždice byly částečně transparentní, je `transparency` možnost nastavena na 0,2 nebo na 20% transparentní.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

![Vrstva dlaždic mapy Google](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Dlaždicovou vrstvu lze přidat k mapě podobným způsobem jako jakékoli jiné vrstvy. Naformátovaná adresa URL, která má zástupné symboly x, y a zoom; `{x}`, `{y}` , v `{z}` uvedeném pořadí, slouží k oznámení vrstvy, kde má být přístup k dlaždicím. Také vrstvy dlaždic v Azure Maps podporu `{quadkey}` , `{bbox-epsg-3857}` a `{subdomain}` zástupné symboly. Chcete-li nastavit, aby byla vrstva dlaždice částečně průhledná, je použita hodnota neprůhlednosti 0,8. Neprůhlednost a průhlednost, i když jsou podobné, používají obrácené hodnoty. Pro převod obou možností odečtěte jejich hodnotu od čísla 1.

> [!TIP]
> V Azure Maps je vhodné vykreslit vrstvy pod jinými vrstvami, včetně základních vrstev mapy. Je také často žádoucí vykreslovat vrstvy dlaždice pod popisky map, aby byly snadno čitelné. `map.layers.add`Metoda přebírá druhý parametr, který je identifikátorem vrstvy, do které chcete vložit novou vrstvu. Chcete-li vložit vrstvu dlaždice pod popisky map, lze použít následující kód: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

![Vrstva Azure Maps dlaždice](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>Zobrazení provozu

Azure Maps i Google Maps mají možnosti pro překrytí dat přenosů.

### <a name="before-google-maps"></a>Před: Google Maps

Pomocí služby Google Maps lze data toku provozu překrývají k mapě tím, že předáte true do `setTrafficEnabled` metody mapy.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

![Přenosy Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Azure Maps poskytuje několik různých možností pro zobrazení provozu. Incidenty přenosu, jako jsou například uzávěry a havárie provozu, lze zobrazit jako ikony na mapě. Tok přenosů a barevně kódované cesty lze na mapě překrývají. Barvy lze upravit tak, aby se zobrazily relativně k zadanému limitu rychlosti, relativně k normálnímu očekávanému zpoždění nebo absolutnímu zpoždění. Data incidentu v Azure Maps se aktualizují každou minutu a data toku se aktualizují každé dvě minuty.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

![Azure Maps provoz](media/migrate-google-maps-android-app/azure-maps-traffic.png)

Přečtěte si další informace o Android SDK Azure Maps.

[Jak používat ovládací prvek mapy pro Android](how-to-use-android-map-control-library.md)