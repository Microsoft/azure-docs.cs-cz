---
title: 'Kurz: Migrace aplikace pro Android | Mapy Microsoft Azure'
description: Postup migrace aplikace pro Android z webu Google Maps na Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 643d48cb931bcec1a8a3385d2ec24a394660c368
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75909188"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migrace aplikace pro Android z Google Maps

Android SDK Azure Maps má rozhraní API, které se velmi podobá webové sadě SDK. Pokud jste provedli jednu z těchto sad SDK, platí mnoho ze stejných konceptů, osvědčených postupů a architektur a měli byste být schopni snadno přenést vaše znalosti z jedné do druhé.

Android SDK Azure Maps podporuje minimální verzi Androidu API 21: Android 5.0.0 (Lupa).

Všechny příklady, které jsou k dispozici v jazyce Java, ale Kotlin se dají použít i Android SDK Azure Maps.

Další informace o vývoji s touto sadou SDK najdete také v [průvodci Azure Maps Android SDK](how-to-use-android-map-control-library.md) .

## <a name="load-a-map"></a>Načtení mapy

Načtení mapy v aplikaci pro Android pomocí Google nebo Azure Maps se skládá z mnoha stejných kroků. Při použití sady SDK musíte:

- Získejte rozhraní API nebo klíč předplatného pro přístup k libovolné platformě.
- Přidejte do aktivity nějaký kód XML k určení, kde má být mapa vykreslena a jak má být rozložena.
- Předejte všechny metody životního cyklu z aktivity, která obsahuje zobrazení mapy, do odpovídajících těch v rámci třídy map. Konkrétně je nutné přeslat následující metody:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Počkejte, než bude mapa připravena, než se pokusí o přístup prostřednictvím kódu programu.

**Před: Google Maps**

Pokud chcete zobrazit mapu pomocí Google Maps SDK pro Android, provede se následující postup:

1.  Ujistěte se, že jsou nainstalované služby Google Play.
2.  Přidejte závislost pro službu Google Maps do souboru **Gradle. Build** modulu: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Přidejte klíč rozhraní API pro Google Maps do oddílu aplikace v souboru **\_API služby google\_Maps** :
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Přidejte do hlavní aktivity fragment mapy:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  V souboru **MainActivity. Java** budete muset přidat importy pro sadu Google Maps SDK. Předejte všechny metody životního cyklu z aktivity, která obsahuje zobrazení mapy, do odpovídajících těch v rámci třídy map. Instanci `MapView` lze načíst z fragmentu mapy pomocí metody `getMapAsync(OnMapReadyCallback)`. `MapView` automaticky inicializuje systém map a zobrazení. Upravte soubor **MainActivity. Java** následujícím způsobem:

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

Při spuštění v aplikaci bude mapový ovládací prvek načten následujícím způsobem.

<center>

![jednoduché mapy Google](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Po: Azure Maps**

Chcete-li zobrazit mapu pomocí sady Azure Maps SDK pro Android, je nutné provést následující kroky:

1. Otevřete soubor **Build. Gradle** nejvyšší úrovně a přidejte následující kód do části **všechny projekty**, blok **úložišť** :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualizujte svou **aplikaci/Build. Gradle** a přidejte do ní následující kód:
    
    1. Ujistěte se, že je **hodnotu minsdkversion** projektu na rozhraní API 21 nebo vyšší.

    2. Do části Android přidejte následující kód:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Aktualizujte svůj blok závislosti a přidejte nový řádek s závislostí implementace pro nejnovější Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Android SDK Azure Maps se pravidelně upgradují a zvyšují. Nejnovější Azure Maps číslo verze implementace můžete získat v dokumentaci [Začínáme s nástrojem pro mapování Androidu](how-to-use-android-map-control-library.md) . Můžete také nastavit číslo verze z "0,2" na "0 +" tak, aby vždy odkazovalo na nejnovější verzi.
    
    4. Přejděte na **soubor** na panelu nástrojů a pak klikněte na **synchronizovat projekt se soubory Gradle**.
3. Přidejte do hlavní aktivity fragment mapy (res \> layout \> Activity\_Main. XML):
    
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
    
    * Přidání importů pro sadu Azure Maps SDK
    * Nastavení ověřovacích informací Azure Maps
    * získání instance mapového ovládacího prvku v metodě **Create**

    Nastavení ověřovacích informací pro třídu `AzureMaps` globálně pomocí metod `setSubscriptionKey` nebo `setAadProperties` ji vytvoří, takže nebudete muset přidávat informace o ověřování do každého zobrazení. 

    Mapový ovládací prvek obsahuje vlastní metody životního cyklu pro správu životního cyklu OpenGL pro Android, které musí být volány přímo z obsažené aktivity. Aby mohla vaše aplikace správně fungovat, zavolejte metody životního cyklu mapového ovládacího prvku, je nutné přepsat následující metody životního cyklu v aktivitě, která obsahuje mapový ovládací prvek a volat příslušnou metodu mapového ovládacího prvku. 

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

Pokud spustíte aplikaci, mapový ovládací prvek bude načten následujícím způsobem.

<center>

![jednoduchý Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Všimněte si, že Azure Maps ovládací prvek podporuje další přiblížení a poskytuje více zobrazení světa.

> [!TIP]
> Pokud používáte emulátor Androidu ve Windows, mapa se nemusí vykreslit kvůli konfliktům s OpenGL a softwarovým vykreslováním s akcelerovanými grafickými nástroji. Pro vyřešení tohoto problému pracovali několik následujících. Otevřete Správce AVD a vyberte virtuální zařízení, které chcete upravit. V části **Emulovaný výkon** nastavte možnost **Grafika** na **hardware**.

## <a name="localizing-the-map"></a>Lokalizace mapy

Pokud jsou vaše cílová skupina rozdělená do několika zemí nebo komunikují s různými jazyky, je lokalizace důležitá.

**Před: Google Maps**

Jazyk mapy lze nastavit v metodě `onCreate` hlavní aktivity přidáním následujícího kódu před nastavením kontextu zobrazení mapy. Následující omezení jazyka na francouzštinu používá kód jazyka "fr".

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

<center>

lokalizace ![Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Po: Azure Maps**

Azure Maps poskytuje tři různé způsoby nastavení jazyka a regionálního zobrazení mapy. První možností je předat informace o jazykovém a regionálním zobrazení do třídy `AzureMaps` pomocí globálně `setLanguage` a `setView` metody. Tím se nastaví výchozí jazyk a místní zobrazení pro všechny ovládací prvky Azure Maps načtené ve vaší aplikaci. Následující omezení jazyka na francouzštinu používá kód jazyka "fr-FR".

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

Druhou možností je předat jazyk a zobrazit informace do XML mapového ovládacího prvku.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Třetí možností je programově nastavit jazyk a regionální zobrazení mapy pomocí metody map `setStyle`. To lze provést kdykoli, chcete-li změnit jazyk a regionální zobrazení mapy.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Tady je příklad Azure Maps s jazykem nastaveným na "fr-FR".

<center>

![Azure Maps lokalizace](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Úplný seznam podporovaných jazyků a regionálních zobrazení je popsán [zde](supported-languages.md).

## <a name="setting-the-map-view"></a>Nastavení zobrazení mapy

Dynamická mapování v mapách Azure i Google je možné programově přesunout do nových geografických umístění voláním vhodných metod. V níže uvedených příkladech se dozvíte, jak se má mapa zobrazit v satelitním notebooku, vycentrovat mapu na umístění s souřadnicemi (zeměpisná šířka: 35,0272, zeměpisná délka:-111,0225) a změnit úroveň přiblížení na 15 v Google Maps.

> [!NOTE]
> Google Maps používá v dimenzích dlaždice, které jsou 256 pixelů, zatímco Azure Maps používá větší dlaždici 512 pixelů. Tím se sníží počet síťových požadavků, které Azure Maps k načtení stejné oblasti rozvržení jako Google Maps. Vzhledem k tomu, jak můžou pyramidy dlaždice pracovat v mapových ovládacích prvcích, jsou větší dlaždice v Azure Maps znamená, že k dosažení stejné zobrazitelné oblasti jako mapy v Google Maps je při použití Azure Maps nutné odečíst úroveň přiblížení v Google Maps pomocí 1.

**Před: Google Maps**

Kameru ovládacího prvku mapy Google Maps se dá programově přesunout pomocí metody `moveCamera`, která umožňuje určit střed mapy a úroveň přiblížení. Metodu `setMapType` lze použít ke změně typu mapy, která se zobrazí.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![zobrazení sady Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**Po: Azure Maps**

Jak bylo uvedeno dříve, aby se dosáhlo stejné zobrazitelné oblasti v Azure Maps, odčítání úrovně přiblížení v Google Maps o jednu, v tomto případě použijte úroveň přiblížení 14.

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

Zobrazení mapy lze aktualizovat programově pomocí `setCamera` map a metod `setStyle`.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

zobrazení ![Azure Maps sady](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Další zdroje informací:**

- [Podporované styly mapy](supported-map-styles.md)

## <a name="adding-a-marker"></a>Přidání značky

Data bodu se často vykreslují na mapě pomocí obrázku na mapě. Tyto obrázky se často označují jako značky, špendlíky, kolíky nebo symboly. Následující příklady vykreslí data bodu jako značky na mapě (zeměpisná šířka: 51,5, zeměpisná délka:-0,2).

**Před: Google Maps**

Pomocí Map Google se značky přidávají pomocí metody map `addMarker`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

](media/migrate-google-maps-android-app/google-maps-marker.png)</center> značka ![mapy Google

**Po: Azure Maps**

Data Azure Maps Point lze na mapě vykreslovat tak, že nejprve přidáte data do zdroje dat a pak tento zdroj dat připojíte ke vrstvě symbolů. Zdroj dat optimalizuje správu prostorových dat v mapovém ovládacím prvku a vrstva symbolů určuje, jak vykreslovat data bodů pomocí jako obrázek nebo text.

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

<center>

Značka Azure Maps ![](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Přidání vlastní značky

Vlastní image lze použít k reprezentaci bodů na mapě. Následující obrázek se používá v níže uvedených příkladech použití vlastní image k zobrazení bodu na mapě (zeměpisná šířka: 51,5, zeměpisná délka:-0,2) a Posune pozici značky tak, aby se bod ikony připínáčku rovnal se správnou polohou na mapě.

<center>

![žlutý obrázek připínáček](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_připínáček. png</center>

V obou příkladech se výše uvedený obrázek přidá do nakreslené složky prostředků aplikací.

**Před: Google Maps**

Pomocí Map Google lze vlastní image použít pro značky jejich načtením pomocí možnosti `icon` značky. Chcete-li zarovnat bod obrázku na souřadnici, lze použít možnost `anchor`. Kotva je relativní vzhledem k rozměrům obrázku, v tomto případě 0,2 jednotek na šířku a 1 jednotka vysoká.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![vlastní značky pro Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Po: Azure Maps**

Vrstvy symbolů v Azure Maps podporují i vlastní image, ale image se nejdřív musí načíst do prostředků mapy a přiřadit jim jedinečné ID. Vrstva symbolů pak může odkazovat na toto ID. Symbol může být posunut pro zarovnání ke správnému bodu na obrázku pomocí možnosti `iconOffset`. Všimněte si, že posun ikony je v pixelech. Ve výchozím nastavení je posun relativní vzhledem k dolnímu středu obrázku, ale lze jej upravit pomocí možnosti `iconAnchor`. Tento příklad nastavuje možnost `iconAnchor` pro `"center"` a používá posun ikony pro přesunutí obrázku o 5 pixelů vpravo a 15 pixelů až do zarovnání s bodem připínáčku.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Azure Maps vlastní značku](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Přidání lomené čáry

Lomené čáry slouží k reprezentaci čáry nebo cesty na mapě. Následující příklady ukazují, jak vytvořit přerušovanou lomenou čáru na mapě.

**Před: Google Maps**

Pomocí služby Google Maps lze vytvořit lomenou čáru pomocí třídy `PolylineOptions` a přidat k mapě pomocí metody `addPolyline`. Barvu tahu lze nastavit pomocí možnosti `color`, šířka tahu je nastavena pomocí možnosti Šířka a pole přerušovaného tahu lze nastavit pomocí možnosti `pattern`.

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

<center>

![](media/migrate-google-maps-android-app/google-maps-polyline.png)</center> na lomenou čáru mapy Google

**Po: Azure Maps**

V Azure Maps se lomené čáry nazývají objekty LineString nebo MultiLineString. Tyto objekty lze přidat do zdroje dat a vykresleny pomocí spojnicové vrstvy. Počítejte s tím, že jednotky šířky a přerušovaného pole "pixel" se zarovnají s Azure Maps Web SDK v, která používá stejné hodnoty v obou sadách SDK, stejné výsledky.

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

<center>

![Azure Maps lomená čáry](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Přidání mnohoúhelníku

Mnohoúhelníky slouží k reprezentaci oblasti na mapě. Následující příklady ukazují, jak vytvořit mnohoúhelník, který tvoří trojúhelník založený na souřadnici středu mapy.

**Před: Google Maps**

Pomocí služby Google Maps lze mnohoúhelník vytvořit pomocí třídy `PolygonOptions` a přidat k mapě pomocí metody `addPolygon`. Barvy výplně a tahu lze nastavit pomocí možnosti `fillColor` a `strokeColor`, šířka tahu je nastavena pomocí možnosti `strokeWidth`.

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

<center>

](media/migrate-google-maps-android-app/google-maps-polygon.png)</center> mnohoúhelníku mapy Google ![

**Po: Azure Maps**

V Azure Maps lze objekty mnohoúhelníku a více mnohoúhelníků přidat do zdroje dat a vykreslit na mapě pomocí vrstev. Oblast mnohoúhelníku se dá vykreslit v mnohoúhelníkové vrstvě. Obrys mnohoúhelníku lze vykreslit pomocí spojnicové vrstvy. Počítejte s tím, že jednotky šířky a přerušovaného pole "pixel" se zarovnají s Azure Maps Web SDK v, která používá stejné hodnoty v obou sadách SDK, stejné výsledky.

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

<center>

![Azure Maps mnohoúhelníku](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Překrytí vrstvy dlaždice

Vrstvy dlaždic, označované také jako překryvy obrázků ve službě Google Maps, umožňují překrýt obrázky vrstev, které byly rozděleny do menších dlaždic, které jsou v souladu se systémem map dlážděn. Jedná se o běžný způsob překrytí imagí vrstev nebo velmi rozsáhlých datových sad.

V následujících příkladech je překrytá vrstva s paprskovou dlaždicí počasí z Iowaho prostředí Mesonet of Iowa State University. Velikost dlaždic je 256 pixelů.

**Před: Google Maps**

S Google Maps může být vrstva dlaždic překryta na mapě pomocí třídy `TileOverlayOptions` a přidána k mapě pomocí metody `addTileLauer`. Chcete-li, aby dlaždice byly poloprůhledné, je možnost `transparency` nastavena na 0,2 nebo na 20% transparentní.

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

<center>

![vrstva dlaždice mapy Google](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Po: Azure Maps**

V Azure Maps lze do mapy přidat vrstvu dlaždice podobným způsobem jako jakákoli jiná vrstva. Naformátovaná adresa URL, která má zástupné symboly x, y a zoom; `{x}`, `{y}``{z}` v uvedeném pořadí slouží k oznámení vrstvy, kde má být přístup k dlaždicím. Vrstvy dlaždic v Azure Maps také podporují zástupné symboly `{quadkey}`, `{bbox-epsg-3857}` a `{subdomain}`. Chcete-li nastavit, aby byla vrstva dlaždice částečně průhledná, je použita hodnota neprůhlednosti 0,8. Všimněte si, že krytí a průhlednost, i když jsou podobné, používají obrácené hodnoty. Převod mezi nimi jednoduše odečte jejich hodnotu od čísla 1.

> [!TIP]
> V Azure Maps vrstev lze snadno vykreslovat pod jinými vrstvami, včetně základních vrstev mapy. Často je žádoucí vykreslovat vrstvy dlaždice pod popisky map, aby byly snadno čitelné. Metoda `map.layers.add` přebírá druhý parametr, který je identifikátorem vrstvy, do které chcete vložit novou vrstvu. Chcete-li vložit vrstvu dlaždice pod popisky map, lze použít následující kód: `map.layers.add(myTileLayer, "labels");`

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

<center>

vrstva dlaždic ![Azure Maps](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Zobrazit provoz

Data přenosů se dají překrývají v Azure i ve službě Google Maps.

**Před: Google Maps**

Díky službě Google Maps je možné data toku provozu překrývají k mapě tím, že se do `setTrafficEnabled`é metody v mapě přejdou true.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![přenosů Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Po: Azure Maps**

Azure Maps poskytuje několik různých možností pro zobrazení provozu. Incidenty přenosu, jako jsou například uzávěry a havárie provozu, lze zobrazit jako ikony na mapě. Tok přenosů, barevně rozlišené silnice lze na mapě překrytí a barvy je možné upravit tak, aby se využívaly relativně k zadanému limitu rychlosti, relativně k normálnímu očekávanému zpoždění nebo absolutnímu zpoždění. Data incidentu v Azure Maps jsou aktualizována každou minutu a data toku každé dvě minuty.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center> Azure Maps provozu ![

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Android SDK Azure Maps.

> [!div class="nextstepaction"]
> [Jak používat ovládací prvek mapy pro Android](how-to-use-android-map-control-library.md)
