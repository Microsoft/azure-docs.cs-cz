---
title: Přidat vrstvu symbolů do systému Android mapy v Azure Maps | Dokumentace Microsoftu
description: Postup přidání symboly mapy pomocí Azure Maps Android SDK
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: add6e23d023753e217c102dc946837a71a64c781
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64871074"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Přidat vrstvu symbolů do mapy pomocí Azure Maps Android SDK

Tento článek ukazuje, jak vykreslit data bodu ze zdroje dat jako symbol vrstev na mapě pomocí sady Azure Maps Android SDK.

## <a name="prerequisites"></a>Požadavky

Zcela postupovat podle kroků v tomto článku, je potřeba nainstalovat [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) načíst mapu.

## <a name="add-a-symbol-layer"></a>Přidání vrstvy symbolů

Pokud chcete přidat značku na mapě pomocí symbolu vrstvy, postupujte podle kroků níže:

1. Upravit **res** > **rozložení** > **activity_main.xml** tak, aby vypadala podobně jako následující kód XML:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Zkopírujte následující fragment kódu do **onCreate()** metodu vaše `MainActivity.java` třídy.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    Výše uvedeném fragmentu kódu nejprve získává objekt pomocí instance Azure Maps mapy ovládacího prvku **onReady()** metoda zpětného volání. Poté vytvoří objekt zdroje dat pomocí **DataSource** třídy a přidá jej do mapy. Potom přidá **funkce** obsahující bod geometrie k němu. Obrázek červené značky je nastavíte jako ikona pro symbol. A **symbol vrstvy** používá text nebo ikony k vykreslení dat na základě bodu zabalené ve zdroji dat jako symbol na mapě. Symbol vrstvy se pak vytvoří a zdroj dat je předán do ní chcete zobrazit a se pak přidá do vrstvy mapy.
    
    Po přidání fragmentu kódu výše, váš `MainActivity.java` by měl vypadat jako následující:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
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
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
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
    
V tomto okamžiku při spuštění aplikace byste měli vidět značku na mapě, jak je znázorněno zde:

<center>

![Android mapy kódu pin](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Další postup

Chcete-li přidat další věci do mapy, naleznete v tématu:

> [!div class="nextstepaction"]
> [Přidávání obrazců do Android mapy](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)