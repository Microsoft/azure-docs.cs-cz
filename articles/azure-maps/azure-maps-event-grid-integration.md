---
title: Reakce na události mapování pomocí Event Grid | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak reagovat na události Microsoft Azure Maps pomocí Event Grid.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a89983a9ae45f21deb7a823de049373b4ff9b935
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989055"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reakce na události Azure Maps pomocí Event Grid 

Azure Maps se integruje s Azure Event Grid, aby uživatelé mohli posílat oznámení o událostech jiným službám a aktivovat podřízené procesy. Účelem tohoto článku je pomáhat při konfiguraci obchodních aplikací, aby naslouchaly Azure Maps události. Tato služba umožňuje reagovat na kritické události spolehlivým, škálovatelným a zabezpečeným způsobem. Uživatelé můžou třeba vytvořit aplikaci pro aktualizaci databáze, vytvořit lístek a poslat e-mailové oznámení, když zařízení vstoupí do geografické zóny.

Azure Event Grid je plně spravovaná služba Směrování událostí, která používá model publikování a odběru. Event Grid obsahuje integrovanou podporu pro služby Azure, jako je [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) a [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview). Může doručovat výstrahy událostí na služby mimo Azure pomocí webhooků. Úplný seznam obslužných rutin událostí, které Event Grid podporuje, najdete v [úvodu k Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).


![Azure Event Grid funkční model](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Typy Azure Mapsch událostí

Event Grid používá [odběry událostí](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) ke směrování zpráv událostí odběratelům. Účet Azure Maps emituje následující typy událostí: 

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Vyvolá se, když se přijaté souřadnice přesunuly mimo danou geografickou oblast do v rámci. |
| Microsoft.Maps.GeofenceExited | Vyvolá se, když se přijaté souřadnice přesunuly z dané geografické oblasti na vnější. |
| Microsoft.Maps.GeofenceResult | Vyvoláno pokaždé, když dotaz monitorování geografických zón vrátí výsledek bez ohledu na stav. |

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

## <a name="tips-for-consuming-events"></a>Tipy pro náročné události

Aplikace, které zpracovávají Azure Maps události geografického plotu, by měly dodržovat několik doporučených postupů:

* Více předplatných lze nakonfigurovat pro směrování událostí do stejné obslužné rutiny události. Je důležité, aby se nepředpokládalo, že události jsou z konkrétního zdroje. Vždy zkontrolujte téma zprávy a ověřte, zda pochází ze zdroje, který jste očekávali.
* Zprávy mohou být doručeny mimo pořadí nebo po zpoždění. Použijte pole `X-Correlation-id` v hlavičce odpovědi, abyste pochopili, jestli jsou informace o objektech v aktuálním stavu.
* Při volání metody Get a POST rozhraní API pro monitorování geografických zón se `EnterAndExit`pro každou geometrii v geografickém stavu, pro který se změnil stav z předchozího volání rozhraní API geografického ohraničení, vygeneruje událost Enter nebo Exit.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat monitorování geografických zón k řízení operací v konstrukci webu, najdete tady:

> [!div class="nextstepaction"] 
> [Nastavení geografické zóny pomocí Azure Maps](tutorial-geofence.md)
