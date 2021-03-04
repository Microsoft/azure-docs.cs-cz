---
title: Vytvoření zdroje dat pro mapy Androidu | Mapy Microsoft Azure
description: 'Zjistěte, jak vytvořit zdroj dat pro mapu. Seznamte se se zdroji dat, které Azure Maps Android SDK používá: mikrojson Sources a Vector dlaždice.'
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e870134e2ecd431aa3e5c02638120027f0d47df2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101456"
---
# <a name="create-a-data-source-android-sdk"></a>Vytvoření zdroje dat (Android SDK)

Azure Maps Android SDK ukládá data do zdrojů dat. Použití zdrojů dat optimalizuje datové operace pro dotazování a vykreslování. V současné době existují dva typy zdrojů dat:

- Geografická **zdrojová** data: Správa nezpracovaných dat umístění ve formátu geografických JSON místně. Vhodný pro malé až střední datové sady (nahoru z stovek tisíců tvarů).
- **Zdroj vektorové dlaždice**: načte data formátovaná jako vektorové dlaždice pro aktuální zobrazení mapy na základě systému mapy dláždění. Ideální pro velké až obrovský datové sady (miliony nebo miliardy tvarů).

## <a name="geojson-data-source"></a>Zdroj dat pro injson

Azure Maps používá jako jeden z jeho primárních datových modelů označení typu injson. Injson je otevřený geoprostorové standardní způsob, který představuje geoprostorové údaje ve formátu JSON. Třídy pro věkové třídy JSON dostupné ve Azure Maps Android SDK k snadnému vytváření a serializaci dat typu data v angličtině. Načtěte a uložte data o injson ve `DataSource` třídě a vykreslete je pomocí vrstev. Následující kód ukazuje, jak lze vytvořit objekty typu biojson v Azure Maps.

::: zone pivot="programming-language-java-android"

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add a property to the feature.
feature.addStringProperty("custom-property", "value")

//Add the feature to the data source.
source.add(feature)
```

::: zone-end

Alternativně lze vlastnosti načíst do JsonObject a pak je předávat do funkce při jejím vytváření, jak je znázorněno níže.

::: zone pivot="programming-language-java-android"

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a JsonObject to store properties for the feature.
val properties = JsonObject()
properties.addProperty("custom-property", "value")

val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties)
```

::: zone-end

Jakmile máte vytvořenou funkci s využitím funkce s kojson, můžete do mapy přidat zdroj dat prostřednictvím `sources` vlastnosti mapy. Následující kód ukazuje, jak vytvořit `DataSource` , přidat ho na mapu a přidat funkci do zdroje dat.

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

Následující kód ukazuje několik způsobů, jak vytvořit funkci pro zobrazení typu "geometrií. JSON", Funkcecollection a.

::: zone pivot="programming-language-java-android"

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//GeoJSON Point Geometry
val point = Point.fromLngLat(LONGITUDE, LATITUDE)

//GeoJSON Point Geometry
val linestring = LineString.fromLngLats(PointList)

//GeoJSON Polygon Geometry
val polygon = Polygon.fromLngLats(listOfPointList)

val polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject, innerLineStringObject)

//GeoJSON MultiPoint Geometry
val multiPoint = MultiPoint.fromLngLats(PointList)

//GeoJSON MultiLineString Geometry
val multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList)

val multiLineString = MultiLineString.fromLineString(singleLineString)

//GeoJSON MultiPolygon Geometry
val multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList)

val multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon)

val multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList)

//GeoJSON Feature
val pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE))

//GeoJSON FeatureCollection 
val featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature)

val featureCollection = FeatureCollection.fromFeatures(listOfFeatures)
```

::: zone-end

### <a name="serialize-and-deserialize-geojson"></a>Serializace a deserializace třídyal JSON

Třídy kolekce funkcí, funkce a geometrie mají všechny `fromJson()` a `toJson()` statické metody, které vám pomůžou s serializací. Naformátovaný platný řetězec JSON předaný pomocí `fromJson()` metody vytvoří geometrický objekt. Tato `fromJson()` Metoda také znamená, že můžete použít gson nebo jiné strategie serializace/deserializace. Následující kód ukazuje, jak převzít funkci dokumental JSON a deserializovat ji do třídy funkcí a poté ji serializovat zpátky do řetězce typu "coclass JSON".

::: zone pivot="programming-language-java-android"

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Take a stringified GeoJSON object.
val GeoJSON_STRING = ("{"
        + "      \"type\": \"Feature\","
        + "      \"geometry\": {"
        + "            \"type\": \"Point\""
        + "            \"coordinates\": [-100, 45]"
        + "      },"
        + "      \"properties\": {"
        + "            \"custom-property\": \"value\""
        + "      },"
        + "}")

//Deserialize the JSON string into a feature.
val feature = Feature.fromJson(GeoJSON_STRING)

//Serialize a feature collection to a string.
val featureString = feature.toJson()
```

::: zone-end

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importovat data o nedůležitém formátu JSON ze složky web nebo Assets

Většina souborů. JSON obsahuje Funkcicollection. Číst soubory. JSON jako řetězce a použít `FeatureCollection.fromJson` metodu k jejímu deserializaci.

Následující kód je opětovně použitelnou třídou pro import dat ze složky web nebo Local assets jako řetězec a jeho vrácení do vlákna uživatelského rozhraní prostřednictvím funkce zpětného volání.

::: zone pivot="programming-language-java-android"

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
import android.content.Context
import android.os.Handler
import android.os.Looper
import android.webkit.URLUtil
import java.net.URL
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors

class Utils {
    companion object {

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            */
        fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit) {
            importData(urlOrFileName, context, callback, null)
        }

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            * @param error A callback function to return errors to.
            */
        public fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit, error: ((String?) -> Unit)?) {
            if (urlOrFileName != null && callback != null) {
                val executor: ExecutorService = Executors.newSingleThreadExecutor()
                val handler = Handler(Looper.getMainLooper())
                executor.execute {
                    var data: String? = null
                    
                    try {
                        data = if (URLUtil.isNetworkUrl(urlOrFileName)) {
                            URL(urlOrFileName).readText()
                        } else { //Assume file is in assets folder.
                            context.assets.open(urlOrFileName).bufferedReader().use{
                                it.readText()
                            }
                        }

                        handler.post {
                            //Ensure the resulting data string is not null or empty.
                            if (data != null && !data.isEmpty()) {
                                callback(data)
                            } else {
                                error!!("No data imported.")
                            }
                        }
                    } catch (e: Exception) {
                        error!!(e.message)
                    }
                }
            }
        }
    }
}
```

::: zone-end

Následující kód ukazuje, jak použít tento nástroj k importu dat typu injson do řetězce a jeho vrácení do vlákna uživatelského rozhraní prostřednictvím zpětného volání. Ve zpětném volání může být řetězcová data serializována do kolekce funkcí typu injson a přidána do zdroje dat. Volitelně můžete aktualizovat kameru mapy a soustředit se na data.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the GeoJSON data and add it to the data source.
Utils.importData("SamplePoiDataSet.json", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
            val fc = FeatureCollection.fromJson(result!!)

        //Add the feature collection to the data source.
        source.add(fc)

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),

            //Padding added to account for pixel size of rendered points.
            padding(20)
        )
    }
```

::: zone-end

## <a name="vector-tile-source"></a>Zdroj vektorové dlaždice

Zdroj vektorové dlaždice popisuje, jak přistupovat k vrstvě vektorové dlaždice. Použijte `VectorTileSource` třídu pro vytvoření instance zdroje vektorové dlaždice. Vrstvy vektorové dlaždice jsou podobné vrstvám dlaždic, ale nejsou stejné. Vrstva dlaždice je rastrový obrázek. Vrstvy vektorové dlaždice jsou komprimovaný soubor ve formátu **PBF** . Tento komprimovaný soubor obsahuje data vektorové mapy a jednu nebo více vrstev. Soubor lze vykreslit a stylovat na straně klienta na základě stylu jednotlivých vrstev. Data ve vektorové dlaždici obsahují geografické funkce ve formě bodů, čar a mnohoúhelníků. Je několik výhod používání vrstev vektorových dlaždic namísto vrstev rastrových dlaždic:

- Velikost souboru vektorové dlaždice je obvykle mnohem menší než ekvivalentní rastrová dlaždice. V takovém případě se používá menší šířka pásma. Znamená nižší latenci, rychlejší mapu a lepší uživatelské prostředí.
- Vzhledem k tomu, že se na klientovi vykreslují vektorové dlaždice, přizpůsobuje se rozlišení zařízení, ve kterém se zobrazují. Výsledkem je, že vykreslené mapy jsou lépe definovány a s jasnými popisky.
- Změna stylu dat ve vektorových mapách nepotřebuje stahovat data znovu, protože nový styl lze použít na straně klienta. Naopak změna stylu vrstvy rastrového dlaždice obvykle vyžaduje načtení dlaždic ze serveru a následné použití nového stylu.
- Vzhledem k tomu, že data jsou doručena v vektorovém formátu, je pro přípravu dat vyžadováno méně zpracování na straně serveru. V důsledku toho je možné novější data zpřístupnit rychleji.

Azure Maps dodržuje [specifikaci vektorové dlaždice Mapbox](https://github.com/mapbox/vector-tile-spec), což je otevřený standard. Azure Maps poskytuje následující služby vektorových dlaždic jako součást platformy:

- [](/rest/api/maps/renderv2/getmaptilepreview)  |  [Podrobnosti o formátu dat](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) v dokumentaci k částem silničních bloků
- [](/rest/api/maps/traffic/gettrafficincidenttile)  |  [Podrobnosti o formátu dat](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles) v dokumentaci k incidentům přenosu
- [](/rest/api/maps/traffic/gettrafficflowtile)  |  [Podrobnosti o formátu dat](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) v dokumentaci toku provozu
- Azure Maps Creator taky umožňuje vytvořit vlastní vektorové dlaždice a získat přístup přes [vykreslování dlaždice získat 2](/rest/api/maps/renderv2/getmaptilepreview) .

> [!TIP]
> Když použijete dlaždice vektorového nebo rastrového obrázku ze služby Azure Maps Renderer s webovou sadou SDK, můžete nahradit `atlas.microsoft.com` zástupným symbolem `azmapsdomain.invalid` . Tento zástupný symbol bude nahrazen stejnou doménou, kterou používá mapa, a automaticky bude automaticky připojovat stejné podrobnosti ověřování. To významně zjednodušuje ověřování pomocí služby vykreslování při použití Azure Active Directory ověřování.

Chcete-li zobrazit data ze zdroje vektorové dlaždice na mapě, připojte zdroj k jedné z vrstev vykreslování dat. Všechny vrstvy, které používají zdroj vektorů, musí `sourceLayer` v možnostech určovat hodnotu. Následující kód načte službu Azure Maps Vector Flow Vector dlaždice jako zdroj vektorové dlaždice a pak ji zobrazí na mapě pomocí spojnicové vrstvy. Tento zdroj dlaždice vektoru má jednu sadu dat ve zdrojové vrstvě s názvem "přenosový tok". Řádková data v této datové sadě obsahují vlastnost s názvem `traffic_level` , která se používá v tomto kódu k výběru barvy a škálování velikosti řádků.

::: zone pivot="programming-language-java-android"

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
val trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}"

//Create a vector tile source and add it to the map.
val source = VectorTileSource(
    tiles(arrayOf(trafficFlowUrl)),
    maxSourceZoom(22)
)
map.sources.add(source)

//Create a layer for traffic flow lines.
val layer = LineLayer(
    source,  //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),  //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),  //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1, 1)
        )
    )
)

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels")
```

::: zone-end

![Mapování s použitím barevně kódovaných cest ukazujících na úrovně toku provozu](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>Připojení zdroje dat ke vrstvě

Data se vykreslují na mapě pomocí vrstev vykreslování. Na jeden zdroj dat může odkazovat jedna nebo více vrstev vykreslování. Následující vrstvy vykreslování vyžadují zdroj dat:

- [Bublinová vrstva](map-add-bubble-layer-android.md) – vykreslí data bodu jako kružnice škálované na mapě.
- [Symbolová vrstva](how-to-add-symbol-to-android-map.md) – vykreslí data bodu jako ikony nebo text.
- [Vrstva Heat mapy](map-add-heat-map-layer-android.md) – vykreslí data bodu jako Heat mapu hustoty.
- [Spojnicová vrstva](android-map-add-line-layer.md) – vykreslí čáru a nebo vykreslí obrys mnohoúhelníků.
- [Mnohoúhelníková vrstva](how-to-add-shapes-to-android-map.md) – vyplní oblast mnohoúhelníku plnou barvou nebo vzorkem obrázku.

Následující kód ukazuje, jak vytvořit zdroj dat, přidat ho do mapy a připojit ho k bublinové vrstvě. A pak importujte data bodu geografického JSON ze vzdáleného umístění do zdroje dat.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a layer that defines how to render points in the data source and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
        val fc = FeatureCollection.fromJson(result!!)
    
        //Add the feature collection to the data source.
        dataSource.add(fc)
    
        //Optionally, update the maps camera to focus in on the data.
        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc)
    
        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20)
        )
    }
```

::: zone-end

Existují další vrstvy vykreslování, které se k těmto zdrojům dat nepřipojují, ale přímo načítají data pro vykreslování.

- [Vrstva dlaždice](how-to-add-tile-layer-android-map.md) – nadmnožinou vrstvu rastrového dlaždice nad mapou.

## <a name="one-data-source-with-multiple-layers"></a>Jeden zdroj dat s více vrstvami

K jednomu zdroji dat lze připojit více vrstev. Existuje mnoho různých scénářů, ve kterých je tato možnost užitečná. Zvažte například scénář, ve kterém uživatel nakreslí mnohoúhelník. Měli byste vykreslit a vyplnit mnohoúhelníkovou oblast, protože uživatel přidá body na mapu. Přidáním čáry se styly pro obrys mnohoúhelníku se usnadní zobrazení hran mnohoúhelníku, když uživatel kreslí. Abychom mohli pohodlně upravovat jednotlivé pozice v mnohoúhelníku, můžeme přidat popisovač, jako je kód PIN nebo značka, nad každou pozici.

![Mapování znázorňující více vrstev vykreslování dat z jednoho zdroje dat](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

Ve většině platforem mapování byste potřebovali objekt mnohoúhelníku, objekt čáry a kód PIN pro každou pozici v mnohoúhelníku. Když se mnohoúhelník změní, budete muset ručně aktualizovat čáru a PIN kódy, které se můžou rychle stát složitou.

V Azure Maps je vše, co potřebujete, jediným mnohoúhelníkem ve zdroji dat, jak je znázorněno v následujícím kódu.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats())

//Create a polygon layer to render the filled in area of the polygon.
val polygonLayer = PolygonLayer(
    source,
    fillColor("rgba(255,165,0,0.2)")
)

//Create a line layer for greater control of rendering the outline of the polygon.
val lineLayer = LineLayer(
    source,
    strokeColor("orange"),
    strokeWidth(2f)
)

//Create a bubble layer to render the vertices of the polygon as scaled circles.
val bubbleLayer = BubbleLayer(
    source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
)

//Add all layers to the map.
map.layers.add(arrayOf<Layer>(polygonLayer, lineLayer, bubbleLayer))
```

::: zone-end

> [!TIP]
> Při přidávání vrstev do mapy pomocí `map.layers.add` metody může být ID nebo instance existující vrstvy předána jako druhý parametr. To by znamenalo, že mapa pro vložení nové vrstvy se přidá pod existující vrstvu. Kromě předání v ID vrstvy tato metoda také podporuje následující hodnoty.
>
> - `"labels"` -Vloží novou vrstvu pod mapovou jmenovku vrstvy.
> - `"transit"` – Vloží novou vrstvu pod vrstvu mapy a přenosové vrstvy.

## <a name="next-steps"></a>Další kroky

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidat Heat mapu](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Ukázky kódu pro web SDK](/samples/browse/?products=azure-maps)