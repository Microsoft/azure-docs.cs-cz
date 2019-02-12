---
title: Reagujte na události Azure Maps pomocí služby Event Grid | Dokumentace Microsoftu
description: Zjistěte, jak reagovat na události Azure Maps pomocí služby Event Grid.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a70011b934398ac4e7f74bb67013e93bb5e86e4e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008507"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagujte na události Azure Maps pomocí služby Event Grid 

Azure Maps se integruje s Azure Event Grid tak, aby mohlo odesílat oznámení událostí k dalším službám a podřízené procesy aktivační procedury. Cílem tohoto článku je vám pomohou nakonfigurovat vaše obchodní aplikace tak, aby naslouchala událostem Azure Maps tak, aby mohly reagovat na kritické události spolehlivé, škálovatelné a zabezpečeným způsobem. Například sestavte aplikaci, která se provede několik akcí, jako je aktualizace databáze, vytvářet lístek a doručování e-mailové oznámení pokaždé, když zařízení přejde monitorové geografické zóny.

Azure Event Grid je plně spravovaná služba Směrování událostí, který používá publikování-odběru modelu. Event Grid obsahuje integrovanou podporu pro služby Azure, jako je [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) a [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview)a může poskytovat výstrahy na události na jiné než Azure services pomocí webhooků. Úplný seznam obslužných rutin událostí, které podporuje Služba Event Grid najdete v tématu [Úvod do služby Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).


![Azure Event Grid funkční modelu](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Typy událostí Azure Maps

Pomocí služby Event grid [odběry událostí](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) pro směrování zpráv událostí pro předplatitele. Účet Azure Maps generuje následující typy událostí: 

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Vyvolá, když souřadnice přijatých přesunuty z mimo danou monitorové geografické zóny pro v rámci |
| Microsoft.Maps.GeofenceExited | Vyvolá, když souřadnice přijatých přesunuty z v rámci danou monitorové geografické zóny na mimo |
| Microsoft.Maps.GeofenceResult | Vyvoláno pokaždé, když monitorování geografických zón dotaz vrátí výsledek, bez ohledu na stav |

## <a name="event-schema"></a>Schéma událostí

Následující příklad zobrazit schéma pro GeofenceResult

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

## <a name="tips-for-consuming-events"></a>Tipy pro používání událostí

Aplikace, které zpracovávají události monitorové geografické zóny Azure Maps postupujte podle několik doporučených postupů:

* Několik předplatných lze nakonfigurovat pro směrování událostí na stejný ovladač událostí. Je důležité, abyste se předpokládá, že jsou události z určitého zdroje. Vždy zkontrolujte zpráv tématu k zajištění, že pocházejí ze zdroje, které jste očekávali.
* Můžete doručování zpráv mimo pořadí nebo po prodlevě. Použití `X-Correlation-id` pole v hlavičce odpovědi pochopit, pokud je aktuální informace o objektech.
* Když Get a POST monitorové geografické zóny API je volána s režimu parametrem nastaveným na `EnterAndExit`, je generována událost Enter nebo ukončení pro každý geometrie v monitorové geografické zóny, pro který se změnil stav z předchozího volání rozhraní API monitorové geografické zóny.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak použít monitorování geografických zón pro operace správy v lokalitě konstrukce, naleznete v tématu:

> [!div class="nextstepaction"] 
> [Nastavení monitorové geografické zóny s využitím Azure Maps](tutorial-geofence.md)