---
title: Azure Maps jako zdroj Event Grid
description: V této části najdete popis vlastností a schématu poskytnutých pro Azure Maps události Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: e879ec3442f2e7912acb450a97079d80d7d95a01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393403"
---
# <a name="azure-maps-as-an-event-grid-source"></a>Azure Maps jako zdroj Event Grid

Tento článek poskytuje vlastnosti a schéma pro události Azure Maps. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](https://docs.microsoft.com/azure/event-grid/event-schema). Nabízí také seznam rychlých startů a kurzů pro použití Azure Maps jako zdroje událostí.

## <a name="event-grid-event-schema"></a>Schéma událostí služby Event Grid

### <a name="available-event-types"></a>Dostupné typy událostí

Účet Azure Maps emituje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. Maps. GeofenceEntered | Vyvolá se, když se přijaté souřadnice přesunuly mimo danou geografickou oblast do v rámci. |
| Microsoft. Maps. GeofenceExited | Vyvolá se, když se přijaté souřadnice přesunuly z dané geografické oblasti na vnější. |
| Microsoft. Maps. GeofenceResult | Vyvoláno pokaždé, když dotaz monitorování geografických zón vrátí výsledek bez ohledu na stav. |

### <a name="event-examples"></a>Příklady událostí

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

### <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| závislosti | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Typ | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | objekt | Data události geografických zón. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| apiCategory | řetězec | Kategorie rozhraní API události |
| apiName | řetězec | Název rozhraní API události |
| issues | objekt | Zobrazí seznam problémů zjištěných během zpracování. Pokud se vrátí nějaké problémy, nevrátí se s odpovědí žádné geometrií. |
| responseCode | číslo | Kód odpovědi HTTP |
| geometrií | objekt | Obsahuje seznam geometrií plotu, který obsahuje polohu souřadnic nebo překrývá searchBuffer kolem pozice. |

Objekt Error se vrátí, když dojde k chybě v rozhraní API služby Maps. Objekt Error má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| error | ErrorDetails |Tento objekt se vrátí, když dojde k chybě v rozhraní API mapy.  |

Objekt ErrorDetails se vrátí, když dojde k chybě v rozhraní API služby Maps. ErrorDetails nebo objekt mají následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| kód | řetězec | Stavový kód HTTP |
| zpráva | řetězec | V případě, že je k dispozici, čitelný popis chyby. |
| innererror | InnerError | Je-li k dispozici, objekt obsahující informace o chybě konkrétní služby. |

InnerError je objekt, který obsahuje informace o chybě specifické pro danou službu. Objekt InnerError má následující vlastnosti: 

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| kód | řetězec | Chybová zpráva |

Objekt geometrií vypisuje ID geometrie geografických zón, jejichž platnost vypršela relativně k času uživatele v žádosti. Objekt geometrií obsahuje geometrické položky s následujícími vlastnostmi: 

| Vlastnost | Typ | Popis |
|:-------- |:---- |:----------- |
| DeviceID | řetězec | ID zařízení |
| délku | řetězec | <p>Vzdálenost od souřadnice k nejbližšímu okraji geografického ohraničení. Kladné znamená, že souřadnice je mimo geografickou oblast. Pokud je souřadnice mimo geografickou oblast, ale více než hodnota searchBuffer od nejbližšího ohraničení geografické oblasti, pak je hodnota 999. Záporné znamená, že souřadnice jsou uvnitř geografického ohraničení. Pokud je souřadnice uvnitř mnohoúhelníku, ale více než hodnota searchBuffer od nejbližšího ohraničení geografického plotu, pak je hodnota-999. Hodnota 999 znamená, že je velmi jistota, že je souřadnice dobře mimo geografickou oblast. Hodnota-999 znamená, že je zajistěte velkou jistotu, že je souřadnice dobře v geografickém rozsahu.<p> |
| geometryid |řetězec | Jedinečné ID identifikuje geometrii geografické plotu. |
| nearestlat | číslo | Zeměpisná šířka nejbližšího bodu geometrie |
| nearestlon | číslo | Zeměpisná délka nejbližšího bodu geometrie |
| udId | řetězec | Jedinečné ID vrácené ze služby nahrávání uživatele při nahrávání geografického ohraničení. Nebude součástí rozhraní API pro monitorování geografických zón. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | řetězec [] | Seznam ID geometrie geografického přístupnosti, jehož platnost vypršela vzhledem k času uživatele v žádosti. |
| geometrií | geometrií [] |Obsahuje seznam geometrií plotu, který obsahuje polohu souřadnic nebo překrývá searchBuffer kolem pozice. |
| invalidPeriodGeofenceGeometryId | řetězec []  | Seznam ID geometrie geograficky, která je v neplatném období vzhledem k času uživatele v žádosti. |
| isEventPublished | Boolean | True, pokud je alespoň jedna událost publikovaná do odběratele události Azure Maps, false, pokud není žádná událost publikovaná do předplatitele události Azure Maps. |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy
|Nadpis  |Popis  |
|---------|---------|
| [Reakce na události Azure Maps pomocí Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Přehled Integrace Azure Maps s Event Grid |
| [Kurz: nastavení geografického plotu](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | V tomto kurzu se seznámíte se základními kroky pro nastavení geografického plotu pomocí Azure Maps. Použijete Azure Event Grid ke streamování výsledků geografické plotu a nastavení oznámení na základě výsledků geografické ploty. |

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).