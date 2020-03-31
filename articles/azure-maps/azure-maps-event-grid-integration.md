---
title: Reakce na události mapy pomocí funkce Event Grid | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak reagovat na události Microsoft Azure Maps pomocí Event Grid.
author: philmea
ms.author: philmea
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9c9483af191e5439af0c0b5e433187d6475c178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335720"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reakce na události Azure Maps pomocí Gridu událostí 

Azure Maps se integruje s Azure Event Grid, takže uživatelé můžou odesílat oznámení o událostech do jiných služeb a spouštět podřízené procesy. Účelem tohoto článku je pomoci vám nakonfigurovat vaše obchodní aplikace pro poslech událostí Azure Maps. To umožňuje uživatelům reagovat na kritické události spolehlivým, škálovatelným a bezpečným způsobem. Uživatelé mohou například vytvořit aplikaci pro aktualizaci databáze, vytvoření lístku a doručení e-mailového oznámení pokaždé, když zařízení vstoupí do geografické zóny.

Azure Event Grid je plně spravovaná služba směrování událostí, která používá model publikování a odběru. Event Grid má integrovanou podporu pro služby Azure, jako jsou [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) a Azure [Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview). Může doručovat upozornění na události pro služby, které nejsou Azure pomocí webhooků. Úplný seznam obslužných rutin událostí, které podporuje Event Grid, najdete [v tématu Úvod do Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).


![Funkční model Azure Event Grid](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Typy událostí Azure Maps

Mřížka událostí používá [odběry událostí](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) ke směrování zpráv událostí odběratelům. Účet Azure Maps vydává následující typy událostí: 

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceZadáno | Zvýšené, když přijaté souřadnice se přesunuly z vnějšku dané geofence do |
| Soubor Microsoft.Maps.Geofenceukončen | Zvýšené, když přijaté souřadnice se přesunuly z daného geofence do vnější |
| Microsoft.Maps.GeofenceVýsledek | Vyvolána pokaždé, když geofencing dotaz vrátí výsledek, bez ohledu na stav |

## <a name="event-schema"></a>Schéma událostí

Následující příklad ukazuje schéma pro GeofenceResult:

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="tips-for-consuming-events"></a>Tipy pro náročné akce

Aplikace, které zpracovávají události geografického zabezpečení Azure Maps, by se měly řídit několika doporučenými postupy:

* Nakonfigurujte více předplatných pro směrování událostí na stejnou obslužnou rutinu události. Je důležité nepředpokládat, že události jsou z určitého zdroje. Vždy zkontrolujte téma zprávy a ujistěte se, že zpráva pochází ze zdroje, který očekáváte.
* Pomocí `X-Correlation-id` pole v hlavičce odpovědi porozumíte, zda jsou informace o objektech aktuální. Zprávy mohou být doručeny mimo pořadí nebo po zpoždění.
* Při get nebo post požadavek v rozhraní API geofence `EnterAndExit`je volána s parametrem mode nastavena na , pak Enter nebo Exit událost je generována pro každou geometrii v geofence, pro které se změnil stav z předchozího volání rozhraní API geofence.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak pomocí geofencingu řídit provoz na staveništi, najdete v těchto tématech:

> [!div class="nextstepaction"] 
> [Nastavení geografické zóny pomocí Azure Maps](tutorial-geofence.md)
