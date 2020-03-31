---
title: Schéma událostí Azure Grid Azure Maps Azure
description: Popisuje vlastnosti a schémata poskytovaná pro události Azure Maps pomocí Azure Event Grid
services: event-grid
author: femila
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: femila
ms.openlocfilehash: 9acef524521e8fac6ce6f8f61e5ff3fbbb81d18d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486355"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Schéma událostí Azure Event Grid pro Azure Maps

Tento článek obsahuje vlastnosti a schéma pro události Azure Maps. Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Dostupné typy událostí

Účet Azure Maps vydává následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceZadáno | Zvýšené, když se přijaté souřadnice přesunuly z vnějšku dané geofence do |
| Soubor Microsoft.Maps.Geofenceukončen | Zvýšené, když se přijaté souřadnice přesunuly z daného geofence do vnějšího |
| Microsoft.Maps.GeofenceVýsledek | Vyvolána pokaždé, když geofencing dotaz vrátí výsledek, bez ohledu na stav |

## <a name="event-examples"></a>Příklady událostí

Následující příklad ukazuje schéma **události GeofenceEntered**

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
| téma | řetězec | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Předmět | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | objekt | Geofencing data událostí. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| apiKategorie | řetězec | Kategorie rozhraní API události. |
| apiName | řetězec | Název rozhraní API události. |
| issues | objekt | Uvádí problémy zjištěné během zpracování. Pokud jsou vráceny nějaké problémy, pak bude vrácena žádná geometrie s odpovědí. |
| responseCode | číslo | Kód odpovědi HTTP |
| Geometrie | objekt | Zobrazí seznam geometrií plotu, které obsahují pozici souřadnic nebo překrývají vyhledávací vyrovnávací paměť kolem pozice. |

Objekt chyby je vrácen, když dojde k chybě v rozhraní MAPY ROZHRANÍ API. Objekt chyby má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| error | ErrorDetails |Tento objekt je vrácen, když dojde k chybě v rozhraní MAPY API  |

Objekt ErrorDetails je vrácen, když dojde k chybě v rozhraní MAPY API. ErrorDetails nebo objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| kód | řetězec | Stavový kód HTTP |
| zpráva | řetězec | Je-li k dispozici, lidský čitelný popis chyby. |
| innererror | Vnitřní chyba | Pokud je k dispozici, objekt obsahující informace specifické pro službu o chybě. |

InnerError je objekt obsahující informace o chybě specifické pro službu. Objekt InnerError má následující vlastnosti: 

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| kód | řetězec | Chybová zpráva |

Geometrie objektu, uvádí ID geometrie geofences, které vypršely vzhledem k času uživatele v požadavku. Objekt geometrie má geometrické položky s následujícími vlastnostmi: 

| Vlastnost | Typ | Popis |
|:-------- |:---- |:----------- |
| Deviceid | řetězec | ID zařízení. |
| Vzdálenost | řetězec | <p>Vzdálenost od souřadnice k nejbližšímu okraji geofence. Pozitivní znamená, že souřadnice je mimo geofence. Pokud je souřadnice mimo geofence, ale více než hodnota searchBuffer od nejbližšího ohraničení geofence, pak je hodnota 999. Negativní znamená, že souřadnice je uvnitř geofence. Pokud je souřadnice uvnitř mnohodalšího, ale více než hodnota searchBuffer od nejbližšího ohraničení geofencingu, pak je hodnota -999. Hodnota 999 znamená, že existuje velká důvěra, že souřadnice je mimo geofence. Hodnota -999 znamená, že existuje velká důvěra, že souřadnice je dobře uvnitř geofence.<p> |
| geometryid |řetězec | Jedinečné id identifikuje geometrii geofence. |
| nearestlat | číslo | Zeměpisná šířka nejbližšího bodu geometrie. |
| nejbližší | číslo | Zeměpisná doba nejbližšího bodu geometrie. |
| udId | řetězec | Jedinečné id se vrátil o službu nahrávání uživatele při nahrávání geofence. Nebudou zahrnuty do geofencing post API. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | řetězec[] | Seznami ID geometrie geofence, která vypršela vzhledem k času uživatele v požadavku. |
| Geometrie | geometrie[] |Zobrazí seznam geometrií plotu, které obsahují pozici souřadnic nebo překrývají vyhledávací vyrovnávací paměť kolem pozice. |
| invalidPeriodGeofenceGeometryId | řetězec[]  | Seznami ID geometrie geofence, která je v neplatnéobdobí vzhledem k času uživatele v požadavku. |
| isEventPublished | Boolean | True pokud alespoň jedna událost je publikována na předplatitele události Azure Maps, false, pokud žádná událost je publikována na předplatitele události Azure Maps. |

## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md)
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).