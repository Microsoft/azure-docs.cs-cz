---
title: 'Kurz: načtení dat typu injson do Azure Maps Android SDK | Mapy Microsoft Azure'
description: Kurz, jak načíst datový soubor pro soubor. JSON do sady Azure Maps sada SDK pro Android map
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b527cd7b3f841b6cb3dcf2dce6930f3bd9bcc184
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681970"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>Kurz: načtení dat typu injson do Azure Maps Android SDK

Tento kurz vás provede procesem importu dat umístění geografického souboru JSON do Azure Maps Android SDK. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidejte Azure Maps do aplikace pro Android.
> * Vytvořte zdroj dat a načtěte ho v souboru. JSON z místního souboru nebo webu.
> * Zobrazte data na mapě.

## <a name="prerequisites"></a>Předpoklady

1. Dokončete [rychlý Start: Vytvoření aplikace pro Android](quick-android-map.md). V tomto kurzu se rozšiřuje kód používaný v tomto rychlém startu.
2. Stáhněte si [ukázkové body důležitého](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) souboru JSON.

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importovat data o nedůležitém formátu JSON ze složky web nebo Assets

Většina souborů. JSON zabalí všechna data v rámci `FeatureCollection` . Je-li to v případě, že soubory. JSON jsou načteny do aplikace jako řetězec, mohou být předány do statické metody kolekce funkcí `fromJson` , která provede deserializaci řetězce do objektu typu objektiv JSON `FeatureCollection` , který lze přidat k mapě.

Následující kroky ukazují, jak naimportovat soubor. JSON do aplikace a deserializovat ho jako objekt pro použití jako soubor `FeatureCollection` . JSON.

1. Dokončete [rychlé zprovoznění: Vytvoření aplikace pro Android](quick-android-map.md) jako následujících kroků sestavích nad touto aplikací.
2. Na panelu projekt v Android studiu klikněte pravým tlačítkem na složku **aplikace** a přejděte na `New > Folder > Assets Folder` .
3. Přetáhněte [ukázkové body důležitého](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) souboru. JSON do složky assety.
4. Vytvořte nový soubor s názvem **utils. Java** a do tohoto souboru přidejte následující kód. Tento kód poskytuje statickou metodu s názvem `importData` , která asynchronně importuje soubor ze `assets` složky aplikace nebo z webu pomocí adresy URL jako řetězce a vrátí jej zpět do vlákna uživatelského rozhraní pomocí jednoduché metody zpětného volání.

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
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

5. Přejít do souboru **MainActivity. Java** a přidat do zpětného volání pro událost následující kód `mapControl.onReady` , který je umístěn uvnitř `onCreate` metody. Tento kód používá nástroj pro import ke čtení v **SamplePoiDataSet.js** souboru jako řetězec a pak ho deserializace jako kolekci funkcí pomocí statické `fromJson` metody `FeatureCollection` třídy. Tento kód také vypočítává oblast ohraničovacího rámečku pro všechna data v kolekci funkcí a používá je k nastavení kamery mapy pro zaměření na data.

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
        this,
        (String result) -> {
            //Parse the data as a GeoJSON Feature Collection.
            FeatureCollection fc = FeatureCollection.fromJson(result);
    
            //Add the feature collection to the data source.
            source.add(fc);
    
            //Optionally, update the maps camera to focus in on the data.
    
            //Calculate the bounding box of all the data in the Feature Collection.
            BoundingBox bbox = MapMath.fromData(fc);
    
            //Update the maps camera so it is focused on the data.
            map.setCamera(
                bounds(bbox),

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. Teď, když je kód pro načtení dat o data ze své třídy do mapy pomocí zdroje dat, je potřeba určit, jak se mají tato data na mapě zobrazovat. K dispozici je několik různých vrstev vykreslování pro data bodů; Nejčastěji používané vrstvy jsou [bublinová vrstva](map-add-bubble-layer-android.md), vrstva [symbolů](how-to-add-symbol-to-android-map.md)a [vrstva Heat mapy](map-add-heat-map-layer-android.md) . Přidejte následující kód, který vykreslí data v bublinové vrstvě ve zpětném volání pro `mapControl.onReady` událost po kódu pro import dat.

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

7. Spusťte aplikaci. Mapa se zobrazí v rámci USA s překrytím kruhů pro každé umístění v souboru geografického souboru JSON.

    ![Mapa USA s daty ze zobrazeného souboru. JSON](media/tutorial-load-geojson-file-android/android-import-geojson.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

K vyčištění prostředků z tohoto kurzu proveďte následující kroky:

1. Zavřete Android Studio a odstraňte aplikaci, kterou jste vytvořili.
2. Pokud jste aplikaci otestovali na externím zařízení, odinstalujte ji z tohoto zařízení.

## <a name="next-steps"></a>Další kroky

Další příklady kódu a prostředí pro interaktivní psaní kódu:

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Zobrazení informací o funkci](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](how-to-add-shapes-to-android-map.md)
