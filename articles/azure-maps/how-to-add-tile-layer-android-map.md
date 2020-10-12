---
title: Přidat dlaždicovou vrstvu do map pro Android | Mapy Microsoft Azure
description: Naučte se, jak přidat vrstvu dlaždice na mapu. Podívejte se na příklad, který používá Android SDK Azure Maps k přidání překrytí paprsků počasí na mapu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 905085d5b0787697d6094bd1337420ee8ae61d90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311083"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Přidání vrstvy dlaždice na mapu pomocí Azure Maps Android SDK

V tomto článku se dozvíte, jak vykreslit vrstvu dlaždice na mapě pomocí Android SDK Azure Maps. Vrstvy dlaždic vám umožní superimpose obrázky nad Azure Maps dlaždice základní mapy. Další informace o Azure Maps systému dlaždic najdete v dokumentaci [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md) .

Vrstva dlaždice se načte do dlaždic ze serveru. Tyto obrázky mohou být předem vykresleny a uloženy jako jakákoli jiná bitová kopie na serveru pomocí zásady vytváření názvů, kterou vrstva dlaždice rozumí. Nebo tyto obrázky můžete vykreslit pomocí dynamické služby, která vygeneruje obrázky v reálném čase. Existují tři různé konvence pojmenování dlaždic, které podporuje Azure Maps třídy TileLayer:

* X, Y, přibližování zápisu na úrovni přiblížení, x je sloupec a Y je pozice dlaždice v mřížce dlaždice.
* Quadkey Notation – kombinace x, y, informace o přiblížení na jednu řetězcovou hodnotu, která je jedinečný identifikátor pro dlaždici.
* Souřadnice ohraničovacího rámečku ohraničovacího rámečku se dají použít k určení obrázku ve formátu `{west},{south},{east},{north}` , který se běžně používá ve [službě mapování webu (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> TileLayer je skvělý způsob, jak vizualizovat velké datové sady na mapě. Z obrázku lze generovat pouze dlaždicovou vrstvu, ale vektorová data lze také vykreslovat jako dlaždicovou vrstvu. Vykreslováním vektorových dat jako dlaždicovou vrstvou musí mapový ovládací prvek načíst pouze dlaždice, jejichž velikost může být mnohem menší než vektorová data, která představují. Tato technika je používána mnoha uživateli, kteří potřebují vykreslit miliony řádků dat na mapě.

Adresa URL dlaždice předaná do vrstvy dlaždice musí být adresa URL protokolu HTTP/HTTPS pro prostředek TileJSON nebo šablona adresy URL dlaždice, která používá následující parametry: 

* `{x}` -X pozice dlaždice. Také potřebuje `{y}` a `{z}` .
* `{y}` -Y pozice dlaždice. Také potřebuje `{x}` a `{z}` .
* `{z}` – Úroveň přiblížení dlaždice Také potřebuje `{x}` a `{y}` .
* `{quadkey}` -Dlaždice quadkey identifikátor založený na konvenci pojmenování systému dlaždice mapy Bing.
* `{bbox-epsg-3857}` – Řetězec ohraničujícího pole ve formátu `{west},{south},{east},{north}` v prostorovém referenčním systému EPSG 3857.
* `{subdomain}` – Zástupný symbol pro hodnoty subdomény, pokud je zadána hodnota subdomény.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit proces v tomto článku, je nutné nainstalovat [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) , aby se načetla mapa.


## <a name="add-a-tile-layer-to-the-map"></a>Přidat na mapu dlaždicovou vrstvu

 Tento příklad ukazuje, jak vytvořit dlaždicovou vrstvu, která odkazuje na sadu dlaždic. Tyto dlaždice používají systém dlážděnní x, y a lupy. Zdrojem této vrstvy dlaždic je překrytí paprsky v podobě počasí z [mesonetu Iowa v oblasti životního prostředí Iowa státní školy](https://mesonet.agron.iastate.edu/ogc/). 

Na mapu můžete přidat vrstvu dlaždice podle následujících kroků.

1. Upravte **rozvržení > rozložení > activity_main.xml** tak, jak vypadá níže:

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

2. Zkopírujte následující fragment kódu níže do metody **Create ()** vaší `MainActivity.java` třídy.

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
    
    Výše uvedený fragment kódu získá Azure Maps instanci ovládacího prvku mapy pomocí zpětného volání metody **Reada ()** . Potom vytvoří `TileLayer` objekt a předá do možnosti adresu URL dlaždice **XYZ** s formátováním `tileUrl` . Neprůhlednost vrstvy je nastavena na hodnotu `0.8` a vzhledem k tomu, že dlaždice ze použité služby dlaždice jsou 256 pixelů, jsou tyto informace předány do `tileSize` Možnosti. Vrstva dlaždice se pak předává do Správce vrstev mapy.

    Po přidání výše uvedeného fragmentu kódu `MainActivity.java` by měl vypadat takto:
    
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

Pokud teď svou aplikaci spustíte, měli byste vidět čáru na mapě, jak vidíte níže:

<center>

![Čára mapy Android](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak nastavit styly mapy, najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Změna stylů mapy v doplňkech Android Maps](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)