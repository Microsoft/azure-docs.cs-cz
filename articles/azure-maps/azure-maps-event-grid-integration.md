---
title: Reakce na události Azure Maps pomocí Event Grid
description: Zjistěte, jak reagovat na Azure Maps události týkající se geografických zón. Podívejte se, jak naslouchat událostem mapy a jak používat Event Grid k přesměrování událostí do obslužných rutin událostí.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 44b2cab814d4e51c4eb0c3ce3322b898f5b26414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92889762"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reakce na události Azure Maps pomocí Event Grid

Azure Maps se integruje s Azure Event Grid, aby uživatelé mohli posílat oznámení o událostech jiným službám a aktivovat podřízené procesy. Účelem tohoto článku je pomáhat při konfiguraci obchodních aplikací, aby naslouchaly Azure Maps události. To umožňuje uživatelům reagovat na kritické události spolehlivým, škálovatelným a zabezpečeným způsobem. Uživatelé můžou třeba vytvořit aplikaci pro aktualizaci databáze, vytvořit lístek a poslat e-mailové oznámení, když zařízení vstoupí do geografické zóny.

Azure Event Grid je plně spravovaná služba Směrování událostí, která používá model publikování a odběru. Event Grid obsahuje integrovanou podporu pro služby Azure, jako je [Azure Functions](../azure-functions/functions-overview.md) a [Azure Logic Apps](../azure-functions/functions-overview.md). Může doručovat výstrahy událostí na služby mimo Azure pomocí webhooků. Úplný seznam obslužných rutin událostí, které Event Grid podporuje, najdete v [úvodu k Azure Event Grid](../event-grid/overview.md).


![Azure Event Grid funkční model](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Typy Azure Mapsch událostí

Event Grid používá [odběry událostí](../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Účet Azure Maps emituje následující typy událostí: 

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. Maps. GeofenceEntered | Vyvoláno, když se přijaté souřadnice přesunuly mimo danou geografickou oblast do v rámci. |
| Microsoft. Maps. GeofenceExited | Vyvoláno, když se přijaté souřadnice přesunuly z dané geografické oblasti na vnější. |
| Microsoft. Maps. GeofenceResult | Vyvoláno pokaždé, když dotaz monitorování geografických zón vrátí výsledek bez ohledu na stav. |

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

## <a name="tips-for-consuming-events"></a>Tipy pro využívání událostí

Aplikace, které zpracovávají Azure Maps události geografického plotu, by měly dodržovat několik doporučených postupů:

* Nakonfigurujte více předplatných pro směrování událostí do stejné obslužné rutiny události. Je důležité, aby se nepředpokládalo, že události jsou z konkrétního zdroje. Vždy zkontrolujte téma zprávy a ujistěte se, že zpráva pochází ze zdroje, který jste očekávali.
* Pomocí `X-Correlation-id` pole v hlavičce odpovědi můžete pochopit, jestli jsou informace o objektech aktuální. Zprávy se můžou doručovat v odlišném pořadí nebo se zpožděním.
* Při volání metody GET nebo POST v rozhraní API geografické plotu s parametrem Mode nastaveným na je `EnterAndExit` vygenerována událost Enter nebo Exit pro každou geometrii v geografickém stavu, pro který byl změněn stav z předchozího volání rozhraní API geografické ploty.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat monitorování geografických zón k řízení operací v konstrukci webu, najdete tady:

> [!div class="nextstepaction"] 
> [Nastavení geografické zóny pomocí Azure Maps](tutorial-geofence.md)