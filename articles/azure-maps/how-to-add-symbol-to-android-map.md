---
title: Přidat vrstvu symbolů do map pro Android | Mapy Microsoft Azure
description: Naučte se, jak přidat značku k mapě. Podívejte se na příklad, který používá Android SDK Azure Maps k přidání vrstvy symbolu, která obsahuje data založená na bodech ze zdroje dat.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: acd5f06a5383308ce736f2860810ebee7e5bce28
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897105"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Přidání vrstvy symbolů na mapu pomocí Azure Maps Android SDK

V tomto článku se dozvíte, jak vykreslovat data bodů ze zdroje dat jako vrstvu symbolů na mapě pomocí Android SDK Azure Maps.

## <a name="prerequisites"></a>Předpoklady

Chcete-li provést úplné provedení kroků v tomto článku, je nutné nainstalovat [Azure Maps Android SDK](./how-to-use-android-map-control-library.md) , aby se načetla mapa.

## <a name="add-a-symbol-layer"></a>Přidání vrstvy symbolů

Chcete-li přidat značku na mapě pomocí vrstvy symbolů, postupujte podle následujících kroků:

1. Upravit **res**  >  **rozložení** zdrojů  >  **activity_main.xml** tak, aby vypadal jako následující kód XML:
    
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

2. Zkopírujte následující fragment kódu do metody **Create ()** vaší `MainActivity.java` třídy.

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
    
    Výše uvedený fragment kódu získá Azure Maps instanci ovládacího prvku mapy pomocí zpětného volání metody **Reada ()** . Potom vytvoří objekt zdroje dat pomocí třídy **DataSource** a přidá jej do mapy. Pak přidá **funkci** , která obsahuje geometrii bodu. Červený obrázek značky se pak nastaví jako ikona pro symbol. **Symbolová vrstva** používá text nebo ikony pro vykreslení dat na základě bodu zabalených ve zdroji dat jako symbol na mapě. Pak se vytvoří vrstva symbolů a do ní se předává zdroj dat, který vykreslí a přidá se do vrstev mapy.
    
    Po přidání výše uvedeného fragmentu kódu `MainActivity.java` by měl vypadat takto:
    
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
    
V tomto okamžiku, pokud spouštíte aplikaci, měla by se zobrazit značka na mapě, jak je znázorněno zde:

<center>

![PIN mapování pro Android](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>

> [!TIP]
> Ve výchozím nastavení vrstvy symbolů optimalizují vykreslování symbolů skrytím symbolů, které se překrývají. Při přiblížení se budou skryté symboly zobrazovat. Chcete-li tuto funkci zakázat a vykreslit všechny symboly za všech okolností, nastavte `iconAllowOverlap` možnost na `true` .

## <a name="next-steps"></a>Další kroky

Chcete-li přidat k mapě další věci, přečtěte si:

> [!div class="nextstepaction"]
> [Přidání obrazců na mapu pro Android](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Zobrazení informací o funkci](display-feature-information-android.md)