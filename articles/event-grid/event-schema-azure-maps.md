---
title: Schéma události Azure Event Grid Azure Maps
description: V této části najdete popis vlastností a schématu poskytnutých pro Azure Maps události Azure Event Grid
services: event-grid
author: femila
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: femila
ms.openlocfilehash: 9acef524521e8fac6ce6f8f61e5ff3fbbb81d18d
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486355"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Azure Event Grid schéma událostí pro Azure Maps

Tento článek poskytuje vlastnosti a schéma pro události Azure Maps. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Dostupné typy událostí

Účet Azure Maps emituje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Vyvolá se, když se přijaté souřadnice přesunuly mimo danou geografickou oblast do v rámci. |
| Microsoft.Maps.GeofenceExited | Vyvolá se, když se přijaté souřadnice přesunuly z dané geografické oblasti na vnější. |
| Microsoft.Maps.GeofenceResult | Vyvoláno pokaždé, když dotaz monitorování geografických zón vrátí výsledek bez ohledu na stav. |

## <a name="event-examples"></a>Příklady událostí

Následující příklad ukazuje schéma události **GeofenceEntered**

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

Následující příklad ukazuje schéma pro **GeofenceResult** 

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

## <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | string | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| subject | string | Cesta definovaná vydavatelem k předmětu události |
| eventType | string | Jeden z registrovaných typů událostí pro tento zdroj události. |
| eventTime | string | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| id | string | Jedinečný identifikátor události |
| data | objekt | Data události geografických zón. |
| dataVersion | string | Verze schématu datového objektu. Vydavatel definuje verzi schématu. |
| metadataVersion | string | Verze schématu metadat události. Event Grid definuje schéma vlastností nejvyšší úrovně. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| apiCategory | string | Kategorie rozhraní API události |
| apiName | string | Název rozhraní API události |
| problémy | objekt | Zobrazí seznam problémů zjištěných během zpracování. Pokud se vrátí nějaké problémy, nevrátí se s odpovědí žádné geometrií. |
| responseCode | číslo | Kód odpovědi HTTP |
| geometrie | objekt | Obsahuje seznam geometrií plotu, který obsahuje polohu souřadnic nebo překrývá searchBuffer kolem pozice. |

Objekt Error se vrátí, když dojde k chybě v rozhraní API služby Maps. Objekt Error má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| chyba | ErrorDetails |Tento objekt se vrátí, když dojde k chybě v rozhraní API mapy.  |

Objekt ErrorDetails se vrátí, když dojde k chybě v rozhraní API služby Maps. ErrorDetails nebo objekt mají následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| code | string | Stavový kód HTTP |
| zpráva | string | V případě, že je k dispozici, čitelný popis chyby. |
| innererror | InnerError | Je-li k dispozici, objekt obsahující informace o chybě konkrétní služby. |

InnerError je objekt, který obsahuje informace o chybě specifické pro danou službu. Objekt InnerError má následující vlastnosti: 

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| code | string | Chybová zpráva |

Objekt geometrií vypisuje ID geometrie geografických zón, jejichž platnost vypršela relativně k času uživatele v žádosti. Objekt geometrií obsahuje geometrické položky s následujícími vlastnostmi: 

| Vlastnost | Typ | Popis |
|:-------- |:---- |:----------- |
| deviceid | string | ID zařízení |
| vzdálenost | string | <p>Vzdálenost od souřadnice k nejbližšímu okraji geografického ohraničení. Kladné znamená, že souřadnice je mimo geografickou oblast. Pokud je souřadnice mimo geografickou oblast, ale více než hodnota searchBuffer od nejbližšího ohraničení geografické oblasti, pak je hodnota 999. Záporné znamená, že souřadnice jsou uvnitř geografického ohraničení. Pokud je souřadnice uvnitř mnohoúhelníku, ale více než hodnota searchBuffer od nejbližšího ohraničení geografického plotu, pak je hodnota-999. Hodnota 999 znamená, že je velmi jistota, že je souřadnice dobře mimo geografickou oblast. Hodnota-999 znamená, že je zajistěte velkou jistotu, že je souřadnice dobře v geografickém rozsahu.<p> |
| geometryid |string | Jedinečné ID identifikuje geometrii geografické plotu. |
| nearestlat | číslo | Zeměpisná šířka nejbližšího bodu geometrie |
| nearestlon | číslo | Zeměpisná délka nejbližšího bodu geometrie |
| udId | string | Jedinečné ID vrácené ze služby nahrávání uživatele při nahrávání geografického ohraničení. Nebude součástí rozhraní API pro monitorování geografických zón. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | řetězec [] | Seznam ID geometrie geografického přístupnosti, jehož platnost vypršela vzhledem k času uživatele v žádosti. |
| geometrie | geometrií [] |Obsahuje seznam geometrií plotu, který obsahuje polohu souřadnic nebo překrývá searchBuffer kolem pozice. |
| invalidPeriodGeofenceGeometryId | řetězec []  | Seznam ID geometrie geograficky, která je v neplatném období vzhledem k času uživatele v žádosti. |
| isEventPublished | Boolean | True, pokud je alespoň jedna událost publikovaná do odběratele události Azure Maps, false, pokud není žádná událost publikovaná do předplatitele události Azure Maps. |

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).