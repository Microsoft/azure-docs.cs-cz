---
title: Zobrazit data o provozu v mapách pro Android | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak zobrazit data o přenosech na mapě pomocí Android SDK Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: aabe246c343537a42c33d3eaad0bfae3989022fe
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604511"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Zobrazit data o přenosech na mapě (Android SDK)

Data toku a data incidentů jsou dva typy dat přenosů, které lze zobrazit na mapě. V této příručce se dozvíte, jak zobrazit oba typy dat přenosů. Data incidentů se skládají z bodových a řádkových dat pro věci, jako jsou konstrukce, uzavření provozu a nehody. Data toku zobrazují metriky týkající se toku provozu na cestách.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste dokončili kroky v [rychlém startu: vytvoření dokumentu aplikace pro Android](quick-android-map.md) . Bloky kódu v tomto článku lze vložit do `onReady` obslužné rutiny události Maps.

## <a name="show-traffic-on-the-map"></a>Zobrazit provoz na mapě

V Azure Maps jsou k dispozici dva typy dat přenosů:

- Data o incidentech – skládají se z bodů a řádkových dat pro věci, jako jsou konstrukce, uzavření provozu a nehody.
- Data toku – poskytuje metriky toku provozu na cestách. Data toku provozu se často používají k obarvení cest. Barvy jsou založené na tom, kolik přenosů zpomaluje tok, relativně k limitu rychlosti nebo jiné metriky. Existují čtyři hodnoty, které lze předat do `flow` Možnosti provozu mapy.

    |Hodnota toku | Description|
    | :-- | :-- |
    | TrafficFlow. NONE | Nezobrazuje data o přenosech na mapě. |
    | TrafficFlow. relativní | Zobrazuje přenosová data, která se vztahují k rychlosti volného toku provozu. |
    | TrafficFlow.RELATIVE_DELAY | Zobrazí oblasti, které jsou pomalejší než průměrná očekávaná prodleva. |
    | TrafficFlow. absolutní | Zobrazuje absolutní rychlost všech vozidel na cestách. |

Následující kód ukazuje, jak zobrazit data o přenosech na mapě.

::: zone pivot="programming-language-java-android"

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)
```

::: zone-end

Následující snímek obrazovky ukazuje výše uvedený kód pro vykreslování informací o přenosech dat v reálném čase na mapě.

![Mapa znázorňující informace o provozu v reálném čase](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Získat podrobnosti o incidentu provozu

Podrobnosti o incidentu provozu jsou k dispozici ve vlastnostech funkce používané k zobrazení incidentu na mapě. Incidenty přenosu dat se do mapy přidávají pomocí služby Azure Mapsho vektoru incidentů provozu. Formát dat v těchto vektorových dlaždicích, pokud je [zde popsán](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles). Následující kód přidá událost Click na mapu a načte funkci incidentu přenosu, na kterou jste klikli, a zobrazí informační zprávu s některými podrobnostmi.

::: zone pivot="programming-language-java-android"

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)

//Add a click event to the map.
map.events.add(OnFeatureClick { features: List<Feature>? ->
    if (features != null && features.size > 0) {
        val incident = features[0]

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {
            val sb = StringBuilder()
            val incidentType = incident.getStringProperty("incidentType")

            if (incidentType != null) {
                sb.append(incidentType)
            }

            if (sb.length > 0) {
                sb.append("\n")
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed" == incidentType) {
                val from = incident.getStringProperty("from")
                if (from != null) {
                    sb.append(from)
                }
            } else { //Get the description of the traffic incident.
                val description = incident.getStringProperty("description")
                if (description != null) {
                    sb.append(description)
                }
            }

            val message = sb.toString()
            if (message.length > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show()
            }
        }
    }
})
```

::: zone-end

Na následujícím snímku obrazovky vidíte výše informace o přenosech dat v reálném čase na mapě s informační zprávou, která zobrazuje podrobnosti o incidentu.

![Mapa znázorňující informace o přenosech v reálném čase pomocí informační zprávy s informacemi o incidentech](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>Další kroky

V následujících průvodcích se dozvíte, jak na mapu přidat další data:

> [!div class="nextstepaction"]
> [Přidání vrstvy dlaždic](how-to-add-tile-layer-android-map.md)
