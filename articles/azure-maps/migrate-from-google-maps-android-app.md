---
title: 'Kurz: Migrace aplikace pro Android | Mapy Microsoft Azure'
description: Jak migrovat aplikaci pro Android z Map Google do Map Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9514398ec6a84becd1283e4b0975804101b64086
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77209728"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migrace aplikace pro Android z Map Google

Sada Azure Maps Android SDK má rozhraní API, které je podobné webové sady SDK. Pokud jste vyvinuli s jedním z těchto sad SDK, platí mnoho stejných konceptů, osvědčených postupů a architektur.

Sada Azure Maps Android SDK podporuje minimální verzi rozhraní API 21 pro Android: Android 5.0.0 (Lollipop).

Všechny příklady jsou uvedeny v Javě; Kotlin však můžete použít s Azure Maps Android SDK.

Další informace o vývoji s Android SDK podle Azure Maps, najdete v [návodu k tématu pro Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="load-a-map"></a>Načtení mapy

Načítání mapy v aplikaci pro Android pomocí Google nebo Azure Maps se skládá z podobných kroků. Při použití sady SDK je nutné:

- Získejte rozhraní API nebo klíč předplatného pro přístup k oběma platformám.
- Přidejte nějaký XML do aktivity a určete, kde má být mapa vykreslena a jak má být rozložena.
- Přepište všechny metody životního cyklu z aktivity obsahující zobrazení mapy na odpovídající metody ve třídě mapy. Zejména je nutné přepsat následující metody:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Před pokusem o přístup k mapě a její naprogramování počkejte, až bude mapa připravena.

**Před: Mapy Google**

Chcete-li zobrazit mapu pomocí sady Google Maps SDK pro Android, provedete následující kroky:

1.  Zkontrolujte, zda jsou nainstalovány služby Google Play.
2.  Přidejte závislost pro službu Mapy Google do souboru **gradle.build** modulu: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Přidejte klíč Google Maps API do části aplikace souboru **google\_maps\_api.xml:**
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Přidejte fragment mapy k hlavní aktivitě:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  V souboru **MainActivity.java** budete muset importovat sdk Mapy Google. Přepošlete všechny metody životního cyklu z aktivity obsahující zobrazení mapy na odpovídající metody ve třídě mapy. Načíst `MapView` instanci z `getMapAsync(OnMapReadyCallback)` fragmentu mapy pomocí metody. Automaticky `MapView` inicializuje systém map a zobrazení. Upravte soubor **MainActivity.java** takto:

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

Při spuštění aplikace se ovládací prvek mapy načte jako na následujícím obrázku.

<center>

![Jednoduché Mapy Google](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Po: Mapy Azure**

Chcete-li zobrazit mapu pomocí sady Azure Maps SDK pro Android, je třeba provést následující kroky:

1. Otevřete soubor **build.gradle** nejvyšší úrovně a přidejte následující kód do oddílu bloku **všech projektů:**

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualizujte **aplikaci/build.gradle** a přidejte do ní následující kód:
    
    1. Ujistěte se, že váš projekt **minSdkVersion** je na API 21 nebo vyšší.

    2. Do sekce Android přidejte následující kód:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Aktualizujte blok závislostí. Přidejte nový řádek závislostí implementace pro nejnovější sdk Android Azure Maps:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Sada Azure Maps Android SDK se pravidelně upgraduje a vylepšuje. Můžete vidět [Začínáme s ovládacím prvkem mapy Android](how-to-use-android-map-control-library.md) získat nejnovější číslo verze Azure Maps. Můžete také nastavit číslo verze od "0.2" do "0+", aby váš kód vždy ukazoval na nejnovější verzi.
    
    4. Přejděte na panel **u souborů** na panelu nástrojů a klikněte na **Synchronizovat projekt s gradle soubory**.
3. Přidejte fragment mapy k hlavní \> \> aktivitě (aktivita\_rozložení zdrojů main.xml):
    
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

4. V souboru **MainActivity.java** budete muset:
    
    * Importuje sadu Azure Maps SDK
    * Nastavení ověřovacích informací azure maps
    * Získání instance ovládacího prvku mapy v metodě **onCreate**

     Nastavte ověřovací informace `AzureMaps` ve třídě `setSubscriptionKey` `setAadProperties` pomocí metod or. Tato globální aktualizace, ujistěte se, že přidáte ověřovací informace do každého zobrazení.

    Ovládací prvek mapy obsahuje vlastní metody životního cyklu pro správu životního cyklu OpenGL androida. Tyto metody musí být volána přímo z obsažené aktivity. Chcete-li správně volat metody životního cyklu ovládacího prvku mapy, musíte přepsat následující metody životního cyklu v aktivitě, která obsahuje ovládací prvek mapy. Zavolejte příslušnou metodu řízení mapy.

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Upravte soubor **MainActivity.java** takto:
    
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

Pokud spustíte aplikaci, ovládací prvek mapy se načte jako na následujícím obrázku.

<center>

![Jednoduché mapy Azure](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Všimněte si, že ovládací prvek Azure Maps podporuje oddálení více a poskytuje více zobrazení na světě.

> [!TIP]
> Pokud používáte emulátor Android u počítače se systémem Windows, mapa se nemusí vykreslovat kvůli konfliktům s OpenGL a softwarovým zrychleným grafickým vykreslováním. Následující pracoval, pro některé lidi, k vyřešení tohoto problému. Otevřete Správce AVD a vyberte virtuální zařízení, které chcete upravit. V panelu **Ověřit konfiguraci** posuňte dolů. V části **Emulovaný výkon** nastavte možnost **Grafika** na **Hardware**.

## <a name="localizing-the-map"></a>Lokalizace mapy

Lokalizace je důležitá, pokud je okruh uživatelů rozložen do více zemí nebo mluví různými jazyky.

**Před: Mapy Google**

Přidejte následující kód `onCreate` k metodě pro nastavení jazyka mapy. Kód musí být přidán před nastavením zobrazení kontextu mapy. Kód jazyka "fr" omezuje jazyk na francouzštinu.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Zde je příklad Map Google s jazykem nastaveným na "fr".

<center>

![Lokalizace Map Google](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Po: Mapy Azure**

Azure Maps poskytuje tři různé způsoby nastavení jazyka a místní zobrazení mapy. První možností je předat třídy informace o `AzureMaps` jazyku a regionálním zobrazení. Tato možnost používá `setLanguage` `setView` statické a metody globálně. To znamená, že výchozí jazyk a místní zobrazení jsou nastaveny napříč všemi ovládacími prvky Azure Maps načtenými ve vaší aplikaci. Tento příklad nastaví francouzštinu pomocí kódu jazyka "fr-FR".

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

Druhou možností je předat jazyk a zobrazit informace kódu XML ovládacího prvku mapy.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Třetí možností je naprogramovat zobrazení jazyka a `setStyle` regionální mapy pomocí metody mapy. Tato možnost aktualizuje jazyk a místní zobrazení při každém spuštění kódu.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Tady je příklad Azure Maps s jazykem nastaveným na "fr-FR".

<center>

![Lokalizace Map Azure](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Prohlédněte si úplný seznam [podporovaných jazyků](supported-languages.md).

## <a name="setting-the-map-view"></a>Nastavení zobrazení mapy

Dynamické mapy v Mapách Azure i V Mapách Google lze programově přesunout do nových geografických umístění voláním příslušných metod. Udělejme mapu zobrazení satelitníletecké snímky, střed mapy nad umístění s souřadnicemi, a změnit úroveň přiblížení. V tomto příkladu použijeme zeměpisnou šířku: 35.0272, zeměpisnou délku: -111.0225 a úroveň zvětšení 15.

**Před: Mapy Google**

Kameru ovládání mapy Google maps lze programově `moveCamera` přesunout pomocí metody. Metoda `moveCamera` umožňuje určit střed mapy a úroveň přiblížení. Metoda `setMapType` změní typ mapy na zobrazený.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Zobrazení nastavení Map Google](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

> [!NOTE]
> Mapy Google používají dlaždice o rozměrech 256 pixelů, zatímco Azure Maps používají větší dlaždici o velikosti 512 pixelů. Tím se sníží počet síťových požadavků, které Mapy Azure potřebují k načtení stejné oblasti mapy jako Mapy Google. Chcete-li dosáhnout stejné viditelné oblasti jako mapa v Mapách Google, musíte při používání Map Azure odečíst úroveň přiblížení používanou v Mapách Google o jednu. 

**Po: Mapy Azure**

Jak již bylo uvedeno dříve, chcete-li dosáhnout stejné zobrazitelné oblasti v Azure Maps odečíst úroveň zvětšení používané v Mapách Google o jeden. V takovém případě použijte úroveň zvětšení 14.

Počáteční zobrazení mapy lze nastavit v atributech XML v ovládacím prvku mapy.

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

Zobrazení mapy lze naprogramovat `setCamera` pomocí `setStyle` map a metod.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Zobrazení nastavení Map Azure](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Další prostředky:**

- [Podporované styly map](supported-map-styles.md)

## <a name="adding-a-marker"></a>Přidání značky

Data bodů jsou často vykreslena pomocí obrázku na mapě. Tyto obrázky jsou označovány jako značky, připínání, špendlíky nebo symboly. Následující příklady vykreslují bodová data jako značky na mapě v zeměpisné šířce: 51.5, zeměpisná délka: -0.2.

**Před: Mapy Google**

Pomocí Map Google se značky `addMarker` přidávají metodou map.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Značka Map Google](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Po: Mapy Azure**

V Azure Maps vykreslujte data bodů na mapě tak, že je nejdřív přidáte do zdroje dat. Potom připojte tento zdroj dat k vrstvě symbolů. Zdroj dat optimalizuje správu prostorových dat v ovládacím prvku mapy. Vrstva symbolů určuje, jak vykreslit data bodů pomocí jako obraz nebo text.

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

![Značka Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Přidání vlastní značky

Vlastní obrázky lze použít k reprezentaci bodů na mapě. Mapa v níže uvedených příkladech používá vlastní obrázek k zobrazení bodu na mapě. Bod je v zeměpisné šířce: 51,5 a délka: -0,2. Kotva odsadí polohu značky tak, aby se bod ikony připínáčku zarovnal se správnou polohou na mapě.

<center>

![žlutý připínáček](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

V obou příkladech je výše uvedený obrázek přidán do čerpatelné složky prostředků aplikací.

**Před: Mapy Google**

V Mapách Google lze pro značky použít vlastní obrázky. Načtěte vlastní obrázky `icon` pomocí volby značky. Chcete-li zarovnat bod obrazu ke `anchor` souřadnici, použijte tuto volbu. Kotva je relativní vzhledem k rozměrům obrazu. V tomto případě je kotva 0,2 jednotky široká a 1 jednotka vysoká.

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

![Vlastní značka Map Google](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Po: Mapy Azure**

Vrstvy symbolů v Azure Maps podporují vlastní image, ale nejdřív je potřeba obrázek načíst do mapových prostředků a přiřadit jedinečné ID. Potom vrstva symbolu musí odkazovat na toto ID. Odsazením symbolu se zarovná `iconOffset` ke správnému bodu v obraze pomocí volby. Odsazení ikony je v obrazových bodech. Ve výchozím nastavení je posun relativní k dolnímu středu obrazu, ale tuto `iconAnchor` hodnotu odsazení lze upravit pomocí volby. V tomto `iconAnchor` příkladu `"center"`je nastaví možnost . Používá odsazení ikony k přesunutí obrazu o pět obrazových bodů doprava a o 15 pixelů nahoru, aby se zarovnalo s bodem obrázku připínáčku.

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

![Vlastní značka Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Přidání čáry

Předily se používají k reprezentaci čáry nebo cesty na mapě. Následující příklady ukazují, jak vytvořit přerušovanou mez na mapě.

**Před: Mapy Google**

V Mapách Google vykreslete pomocí třídy `PolylineOptions` polynažit. Přidejte polynažitu `addPolyline` do mapy pomocí metody. Nastavte barvu tahu `color` pomocí volby. Nastavte šířku tahu `width` pomocí volby. Přidejte pomlčku tahu `pattern` pomocí volby.

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

![Pointa Map Google](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Po: Mapy Azure**

V Azure Maps se nazývají `LineString` `MultiLineString` velina nebo objekty. Přidejte tyto objekty do zdroje dat a vykreslete je pomocí čárové vrstvy. Nastavte šířku tahu `strokeWidth` pomocí volby. Přidejte pomlčku tahu `strokeDashArray` pomocí volby.

Šířka tahu a jednotky "pixel" pole pomlčky v azure maps web sdk, je stejný jako ve službě Mapy Google. Oba přijmout stejné hodnoty k dosažení stejných výsledků.

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

![Předlavená čára Azure Maps](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Přidání polygonu

Polygony se používají k reprezentaci oblasti na mapě. Další příklady ukazují, jak vytvořit polygon. Tento polygon tvoří trojúhelník založený na středové souřadnici mapy.

**Před: Mapy Google**

V Mapách Google vykreslete polygon pomocí `PolygonOptions` třídy. Přidejte polygon do mapy `addPolygon` pomocí metody. Nastavte barvy výplně a `fillColor` `strokeColor` tahu pomocí voleb a. Nastavte šířku tahu `strokeWidth` pomocí volby.

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

![Polygon map Google](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Po: Mapy Azure**

V Azure Maps `Polygon` `MultiPolygon` přidejte a objekty do zdroje dat a vykreslete je na mapě pomocí vrstev. Vykreslení oblasti polyga v polygonové vrstvě. Vykreslete obrys polygonu pomocí čárové vrstvy. Nastavte barvu a šířku `strokeColor` `strokeWidth` tahu pomocí voleb a.

Šířka tahu a jednotky "pixel" pole pomlčka v Azure Maps Web SDK zarovnat s příslušnými jednotkami v Mapách Google. Oba přijmout stejné hodnoty a dosáhnout stejných výsledků.

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

![Polygon map Azure](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Překrytí vrstvy dlaždic

 Pomocí vrstev dlaždic můžete překrýt obrazy vrstev vrstvy, které byly rozděleny na menší dlaždicové obrazy, které jsou zarovnány se systémem dlaždic map. Tento přístup je běžný způsob překrytí obrazů vrstev nebo velkých datových sad. Vrstvy dlaždic se v Mapách Google označují jako překryvy obrázků.

Následující příklady překrýt počasí radar dlaždice vrstva z Iowa životního prostředí Mesonet z Iowa State University. Dlaždice mají velikost 256 pixelů.

**Před: Mapy Google**

S Mapami Google lze vrstvu dlaždic překrytou v horní části mapy. Použijte `TileOverlayOptions` třídu. Přidejte vrstvu dlaždice do `addTileLauer` mapy pomocí metody. Chcete-li dlaždice poloprůhledné, `transparency` je tato možnost nastavena na 0,2 nebo 20 % průhledná.

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

![Vrstva dlaždic V Mapách Google](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Po: Mapy Azure**

Vrstvu dlaždic lze do mapy přidat podobným způsobem jako kterákoli jiná vrstva. Formátovaná adresa URL se zástupnými symboly x, y a lupy; `{x}`, `{y}` `{z}` , se používá k tomu, aby se vrstvě řeklo, kam má přístup k dlaždicím. Vrstvy dlaždic v Azure `{quadkey}` `{bbox-epsg-3857}`Maps `{subdomain}` také podporují a zástupné symboly. Aby byla vrstva dlaždice poloprůhledná, použije se hodnota krytí 0,8. Krytí a průhlednost, i když podobné, použijte obrácené hodnoty. Chcete-li převést mezi oběma možnostmi, odečtěte jejich hodnotu od čísla jedna.

> [!TIP]
> V Azure Maps je vhodné vykreslovat vrstvy pod ostatními vrstvami, včetně základních mapových vrstev. Také je často žádoucí vykreslit vrstvy dlaždic pod popisky mapy, aby byly snadno čitelné. Metoda `map.layers.add` má druhý parametr, který je id vrstvy, do které chcete vložit novou vrstvu níže. Chcete-li vložit vrstvu dlaždice pod popisky mapy, lze použít následující kód:`map.layers.add(myTileLayer, "labels");`

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

![Vrstva dlaždic Azure Maps](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Zobrazení provozu

Mapy Azure i Mapy Google mají možnost překrýt údaje o provozu.

**Před: Mapy Google**

S Mapami Google mohou být údaje o toku provozu překryty `setTrafficEnabled` v horní části mapy předáním metody mapy.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Návštěvnost Map Google](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Po: Mapy Azure**

Azure Maps poskytuje několik různých možností pro zobrazení provozu. Dopravní nehody, jako jsou uzavírky silnic a nehody, mohou být zobrazeny jako ikony na mapě. Dopravní tok a barevně odlišené silnice mohou být překryty na mapě. Barvy lze upravit tak, aby se zobrazovaly vzhledem k zaúčtovanému rychlostnímu limitu vzhledem k normálnímu očekávanému zpoždění nebo absolutnímu zpoždění. Data incidentů v Azure Maps se aktualizují každou minutu a data toku se aktualizují každé dvě minuty.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Provoz na Azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o sdk Android u Azure Maps.

> [!div class="nextstepaction"]
> [Jak používat ovládací prvek mapy Android](how-to-use-android-map-control-library.md)
