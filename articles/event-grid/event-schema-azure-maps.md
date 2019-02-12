---
title: Schéma událostí služby Azure Event Grid Azure Maps
description: Popisuje vlastnosti a schéma, které jsou k dispozici pro události Azure Maps pomocí služby Azure Event Grid
services: event-grid
author: walsehgal
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: v-musehg
ms.openlocfilehash: 74a3674e632f8dc3f0755bc2ad48376708c7966f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008381"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Schéma událostí Azure Event Grid pro mapy Azure

Tento článek obsahuje vlastnosti a schéma pro události Azure Maps. Úvod do schémata událostí, naleznete v tématu [schéma událostí služby Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Typy událostí k dispozici

Účet Azure Maps generuje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Vyvolá, když souřadnice přijatých přesunuty z mimo danou monitorové geografické zóny pro v rámci |
| Microsoft.Maps.GeofenceExited | Vyvolá, když souřadnice přijatých přesunuty z v rámci danou monitorové geografické zóny na mimo |
| Microsoft.Maps.GeofenceResult | Vyvoláno pokaždé, když monitorování geografických zón dotaz vrátí výsledek, bez ohledu na stav |

## <a name="event-examples"></a>Příklady událostí

Následující příklad ukazuje schématu **GeofenceEntered** událostí

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

Následující příklad ukazuje schématu pro **GeofenceResult** 

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

Událost má následující dat nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| předmět | řetězec | Vydavatel definované cesta předmět události. |
| eventType | řetězec | Jeden z typů registrované události pro tento zdroj událostí. |
| čas události | řetězec | Vygenerování události podle času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor pro událost. |
| data | objekt | Data události monitorování geografických zón. |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| metadataVersion | řetězec | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| apiCategory | řetězec | Rozhraní API kategorie události. |
| ApiName | řetězec | Název rozhraní API události. |
| Problémy | objekt | Uvádí problémy během zpracovávání. Pokud jsou vráceny všechny problémy, pak nebudou žádná geometrie odpověď se vrátí. |
| responseCode | číslo | Kód odpovědi HTTP |
| Geometrie | objekt | Seznamy ohrazení geometrie, které obsahují souřadnice umístění nebo překrývat searchBuffer kolem pozici. |

Objekt error je vrácena, když dojde k chybě v rozhraní API pro mapy. Objekt error má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| error | Detaily chyby |Tento objekt je vrácen, pokud dojde k chybě v rozhraní API pro mapy  |

Když dojde k chybě v rozhraní API pro mapy, je vrácen objekt detaily chyby. Detaily chyby nebo objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| kód | řetězec | Stavový kód HTTP. |
| zpráva | řetězec | Pokud je k dispozici, lidské čitelný popis chyby. |
| innererror | InnerError | Pokud je k dispozici, objekt, který obsahuje konkrétní službu informace o této chybě. |

InnerError je objekt, který obsahuje konkrétní službu informace o této chybě. Objekt InnerError má následující vlastnosti: 

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| kód | řetězec | Chybová zpráva. |

Objekt geometrie uvádí geometrie ID monitorovaná geografická zóna, které vypršely relativní vůči času uživatele v požadavku. Geometrie objektu má geometrie položky s následujícími vlastnostmi: 

| Vlastnost | Typ | Popis |
|:-------- |:---- |:----------- |
| ID zařízení | řetězec | ID zařízení. |
| vzdálenost | řetězec | <p>Vzdálenost od souřadnice na nejbližší okraj monitorové geografické zóny. Pozitivní znamená, že bod se souřadnicemi je mimo monitorové geografické zóny. Pokud bod se souřadnicemi je mimo monitorové geografické zóny, ale větší než hodnota searchBuffer mimo hranice nejbližší monitorové geografické zóny, hodnota je 999. Negativní znamená, že bod se souřadnicemi je uvnitř monitorové geografické zóny. Pokud bod se souřadnicemi je uvnitř mnohoúhelníku, ale větší než hodnota searchBuffer mimo hranice nejbližší monitorování geografických zón, hodnota je-999. Hodnota 999 znamená, že je skvělé spolehlivosti bod se souřadnicemi je také mimo monitorové geografické zóny. Hodnota-999 znamená, že je skvělé spolehlivosti bod se souřadnicemi je dobře uvnitř monitorové geografické zóny.<p> |
| geometryid |řetězec | Jedinečné id identifikuje geometrie monitorové geografické zóny. |
| nearestlat | číslo | Zeměpisná šířka nejbližší bod geometrii. |
| nearestlon | číslo | Zeměpisná délka nejbližší bod geometrii. |
| udId | řetězec | Jedinečné id vrácená služba nahrávání uživatele při nahrávání monitorové geografické zóny. Nebudou zahrnuty v rozhraní API příspěvku monitorování geografických zón. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | řetězec] | Seznam ID geometrie monitorové geografické zóny, který je relativní vůči času uživatele v požadavku vypršel. |
| Geometrie | [] geometrie |Seznamy ohrazení geometrie, které obsahují souřadnice umístění nebo překrývat searchBuffer kolem pozici. |
| invalidPeriodGeofenceGeometryId | řetězec]  | Seznam ID geometrie monitorové geografické zóny, který je v neplatná perioda. relativní vůči času uživatele v požadavku. |
| isEventPublished | Boolean | Hodnota TRUE alespoň jednu událost je publikování na Azure Maps odběratel událostí, false, pokud žádná událost se publikuje na předplatitele Azure Maps události. |

## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).