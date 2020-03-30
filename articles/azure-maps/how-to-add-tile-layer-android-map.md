---
title: Přidání vrstvy dlaždic do map Android | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak vykreslit vrstvu dlaždic na mapě pomocí sady Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f98598bd1307bb1b46ff23814780c5f809b9ac90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335564"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Přidání vrstvy dlaždic do mapy pomocí sady Azure Maps SDK Pro Android

Tento článek ukazuje, jak vykreslit vrstvu dlaždic na mapě pomocí sady Azure Maps Android SDK. Vrstvy dlaždic umožňují překrývat obrázky nad dlaždicemi základní mapy Azure Maps. Další informace o systému dlaždic Azure Maps najdete v [dokumentaci k úrovním lupy a k dlaždicové mřížce.](zoom-levels-and-tile-grid.md)

Vrstva dlaždice se načítá v dlaždicích ze serveru. Tyto obrazy mohou být předem vykresleny a uloženy jako všechny ostatní obrázky na serveru pomocí konvence pojmenování, které rozumí vrstva dlaždice. Nebo tyto obrázky mohou být vykresleny pomocí dynamické služby, která generuje obrázky téměř v reálném čase. Existují tři různé dlaždice služby pojmenování zásad podporovaných Azure Maps TileLayer třídy:

* X, Y, Zoom notace - Na základě úrovně zvětšení, x je sloupec a y je řádek pozice dlaždice v mřížce dlaždice.
* Zápis quadkey - Kombinace x, y, zvětšení informací do hodnoty jednoho řetězce, který je jedinečným identifikátorem dlaždice.
* Ohraničovací rámeček - Souřadnice ohraničovacího rámečku lze použít k určení obrazu ve formátu, `{west},{south},{east},{north}` který běžně používají [služby WMS (Web Mapping Services).](https://www.opengeospatial.org/standards/wms)

> [!TIP]
> TileLayer je skvělý způsob, jak vizualizovat velké sady dat na mapě. Nejen, že vrstva dlaždice může být generována z obrazu, ale vektorová data mohou být také vykreslena jako vrstva dlaždic. Vykreslováním vektorových dat jako vrstvy dlaždic musí ovládací prvek mapy načíst pouze dlaždice, které mohou být mnohem menší než vektorová data, která představují. Tato technika je používána mnoha, kteří potřebují vykreslit miliony řádků dat na mapě.

Adresa URL dlaždice předaná do vrstvy dlaždice musí být adresa URL http/https pro prostředek TileJSON nebo šablona adresy URL dlaždice, která používá následující parametry: 

* `{x}`- X pozice dlaždice. Také `{y}` potřeby `{z}`a .
* `{y}`- Pozice dlaždice. Také `{x}` potřeby `{z}`a .
* `{z}`- Úroveň přiblížení dlaždice. Také `{x}` potřeby `{y}`a .
* `{quadkey}`- Dlaždice quadkey identifikátor založený na službě Bing Maps dlaždice systému pojmenování konvence.
* `{bbox-epsg-3857}`- Ohraničovací řetězec `{west},{south},{east},{north}` s formátem v prostorovém referenčním systému EPSG 3857.
* `{subdomain}`- Zástupný symbol pro hodnoty subdomény, pokud je zadána hodnota subdomény.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit proces v tomto článku, je třeba nainstalovat [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) načíst mapu.


## <a name="add-a-tile-layer-to-the-map"></a>Přidání vrstvy dlaždic do mapy

 Tato ukázka ukazuje, jak vytvořit vrstvu dlaždic, která odkazuje na sadu dlaždic. Tyto dlaždice používají "x, y, zoom" dlažba systému. Zdrojem této vrstvy dlaždice je překrytí meteorologického radaru z [Iowa Environmental Mesonet z Iowa State University](https://mesonet.agron.iastate.edu/ogc/). 

Vrstvu dlaždic můžete do mapy přidat podle následujících kroků.

1. Upravit **res > rozložení > activity_main.xml** tak, aby to vypadalo jako ten níže:

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Zkopírujte následující fragment kódu níže do metody **onCreate()** vaší `MainActivity.java` třídy.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    Fragment kódu výše výše získá instanci řízení mapy Azure Maps pomocí metody zpětného volání **onReady().** Potom vytvoří `TileLayer` objekt a předá formátované **xyz** dlaždice URL do možnosti. `tileUrl` Krytí vrstvy je nastaveno `0.8` tak, aby dlaždice ze služby dlaždic, které se používají, `tileSize` byly dlaždice 256 pixelů, jsou tyto informace předány do této možnosti. Vrstva dlaždic je pak předána do správce vrstvy mapy.

    Po přidání fragmentu kódu výše, `MainActivity.java` by měl vypadat jako ten níže:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
    public class MainActivity extends AppCompatActivity {
    
        static{
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
    
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
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

Pokud aplikaci spustíte nyní, měli byste vidět řádek na mapě, jak je vidět níže:

<center>

![Mapová čára Androidu](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Další kroky

Další informace o způsobech nastavení stylů mapy najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Změna stylů map v mapách Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)