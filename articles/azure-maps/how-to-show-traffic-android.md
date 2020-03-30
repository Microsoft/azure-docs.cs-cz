---
title: Zobrazit dopravní údaje na mapě android | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak zobrazit data o provozu na mapě pomocí sady Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e5611eeb08ac370e12cf452d57a87e449fbd80da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335380"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Zobrazení provozních dat na mapě pomocí sady Azure Maps SDK pro Android

Data toku a incidenty jsou dva typy provozních dat, které lze zobrazit na mapě. Tato příručka ukazuje, jak zobrazit oba typy provozních dat. Údaje o incidentech se skládají z údajů o bodových a linkových datech pro věci, jako jsou konstrukce, uzavírky silnic a nehody. Údaje o toku zobrazují metriky o toku provozu na silnici.

## <a name="prerequisites"></a>Požadavky

Než budete moci zobrazit provoz na mapě, musíte [vytvořit účet Azure](quick-demo-map-app.md#create-an-account-with-azure-maps)a získat klíč [předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account). Potom je potřeba nainstalovat [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) a načíst mapu.

## <a name="incidents-traffic-data"></a>Údaje o provozu incidentů 

Chcete-li volat, `setTraffic` je třeba importovat `incidents`následující knihovny a:

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 Následující fragment kódu ukazuje, jak zobrazit dopravní data na mapě. Předáme metodě logickou `incidents` hodnotu a předáme ji metodě. `setTraffic` 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Údaje o toku

Chcete-li volat, `setTraffic` musíte nejprve importovat `flow`následující knihovny a:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

K nastavení dat toku provozu použijte následující fragment kódu. Podobně jako kód v předchozí části předáme `flow` `setTraffic` metodě vrácenou hodnotu metody. Existují čtyři hodnoty, které `flow`mohou být předány `flow` do , a každá hodnota by aktivační událost vrátit příslušnou hodnotu. Vrácená hodnota `flow` bude pak předána `setTraffic`jako argument . Následující čtyři hodnoty naleznete v následující tabulce:

| | |
| :-- | :-- |
| TrafficFlow.NONE | Nezobrazuje dopravní data na mapě |
| TrafficFlow.RELATIVNÍ | Zobrazuje dopravní údaje, které jsou relativní vzhledem k rychlosti volného toku na silnici. |
| TrafficFlow.RELATIVE_DELAY | Zobrazí oblasti, které jsou pomalejší než průměrná očekávaná prodleva. |
| TrafficFlow.ABSOLUTE | Zobrazuje absolutní rychlost všech vozidel na silnici |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Zobrazení provozních údajů o incidentech kliknutím na funkci

Chcete-li získat incidenty pro konkrétní funkci, můžete použít níže uvedený kód. Po kliknutí na funkci logika kódu zkontroluje incidenty a vytvoří zprávu o incidentu. V dolní části obrazovky se zobrazí zpráva s podrobnostmi.

1. Nejprve je třeba upravit **rozložení res > > activity_main.xml**, aby vypadalo jako ten níže. Můžete nahradit `mapcontrol_centerLat`, `mapcontrol_centerLng`a `mapcontrol_zoom` požadované hodnoty. Odvolání, úroveň přiblížení je hodnota mezi 0 a 22. Při úrovni přiblížení 0 se celý svět vejde na jednu dlaždici.

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Přidejte následující kód do souboru **MainActivity.java.** Balíček je součástí ve výchozím nastavení, takže se ujistěte, že váš balíček nahoře.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
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

3. Jakmile do aplikace začleníte výše uvedený kód, budete moci kliknout na funkci a zobrazit podrobnosti o dopravních událostech. V závislosti na zeměpisné šířce, zeměpisné délky a hodnotách úrovně přiblížení, které jste použili v souboru **activity_main.xml,** se zobrazí výsledky podobné následujícímu obrázku:

   <center>

   ![Incident-provoz na mapě](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Další kroky

V následujících průvodcích se dozvíte, jak do mapy přidat další data:

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy dlaždic](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Přidání obrazců do mapy androida](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Zobrazení informací o funkci](display-feature-information-android.md)
