---
title: Přidání vrstvy symbolů do map Android | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak vykreslit data bodů na mapě přidáním vrstvy symbolů pomocí sady Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0eca73b5acae715283c05125181e12729ed5a772
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335591"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Přidání vrstvy symbolů do mapy pomocí sady Azure Maps Android SDK

Tento článek ukazuje, jak vykreslit data bodů ze zdroje dat jako vrstvu symbolů na mapě pomocí sady Azure Maps Android SDK.

## <a name="prerequisites"></a>Požadavky

Chcete-li zcela postupovat podle kroků v tomto článku, musíte nainstalovat [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) načíst mapu.

## <a name="add-a-symbol-layer"></a>Přidání vrstvy symbolů

Chcete-li přidat značku na mapu pomocí vrstvy symbolů, postupujte podle následujících kroků:

1. Upravte **rozložení res** > **layout** > **activity_main.xml** tak, aby vypadalo jako následující XML:
    
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

2. Zkopírujte následující fragment kódu do metody **onCreate()** vaší `MainActivity.java` třídy.

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
    
    Fragment kódu výše výše získá instanci řízení mapy Azure Maps pomocí metody zpětného volání **onReady().** Potom vytvoří objekt zdroje dat pomocí **DataSource** třídy a přidá ji do mapy. Potom přidá **Prvek** obsahující geometrii Point. Červený symbol značky je pak nastaven jako ikona symbolu. **Vrstva symbolů** používá text nebo ikony k vykreslení dat založených na bodu zabalených ve zdroji dat jako symbolu na mapě. Poté se vytvoří vrstva symbolů a zdroj dat se jí předá, aby se vykresloval, a poté se přidá do vrstev mapy.
    
    Po přidání fragmentu kódu výše, `MainActivity.java` by měl vypadat jako ten níže:
    
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
    
V tomto okamžiku, pokud spustíte aplikaci, měli byste vidět značku na mapě, jak je znázorněno zde:

<center>

![Android mapový pin](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Další kroky

Další informace na mapu najdete v tématu:

> [!div class="nextstepaction"]
> [Přidání obrazců do mapy Androidu](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Zobrazení informací o funkci](display-feature-information-android.md)