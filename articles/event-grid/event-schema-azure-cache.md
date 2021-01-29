---
title: Mezipaměť Azure pro Redis jako zdroj Event Grid
description: V této části najdete popis vlastností, které jsou k dispozici pro události Redis Azure Event Grid pomocí služby Azure cache.
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055797"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Mezipaměť Azure pro Redis jako zdroj Event Grid

Tento článek poskytuje vlastnosti a schéma pro události Redis v mezipaměti Azure. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md). 

## <a name="event-grid-event-schema"></a>Schéma událostí služby Event Grid

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>Seznam událostí pro Azure cache pro rozhraní REST API Redis

Tyto události se aktivují, když klient exportuje, importuje nebo škáluje voláním Azure cache pro rozhraní REST API Redis. Aktualizace Redis aktivuje událost opravy.

 |Název události |Description|
 |----------|-----------|
 |**Microsoft. cache. ExportRDBCompleted** |Aktivováno při exportu dat do mezipaměti. |
 |**Microsoft. cache. ImportRDBCompleted** |Aktivováno při importu dat do mezipaměti. |
 |**Microsoft. cache. PatchingCompleted** |Aktivuje se po dokončení oprav. |
 |**Microsoft. cache. ScalingCompleted** |Aktivované při dokončení škálování. |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>Obsah odpovědi na událost

Když se aktivuje událost, Služba Event Grid odešle data o této události do předplatného koncového bodu.

V této části najdete příklad toho, jak by tato data vypadala jako u každé události Azure cache pro Redis.

### <a name="microsoftcachepatchingcompleted-event"></a>Událost Microsoft. cache. PatchingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Událost Microsoft. cache. ImportRDBCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Událost Microsoft. cache. ExportRDBCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft. cache. ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| subject | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| eventType | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | object | Mezipaměť Azure pro data události Redis |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| časové razítko | řetězec | Čas, kdy došlo k události. |
| name | řetězec | Název události. |
| status | řetězec | Stav události Selhalo nebo bylo úspěšné. |


## <a name="quickstarts"></a>Rychlé starty

Pokud chcete vyzkoušet Azure cache pro události Redis, přečtěte si některý z těchto článků rychlý Start:

|Pokud chcete použít tento nástroj:    |Viz tento článek: |
|--|-|
|Portál Azure Portal    |[Rychlý Start: směrování mezipaměti Azure pro události Redis do webového koncového bodu pomocí Azure Portal](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Rychlý Start: směrování mezipaměti Azure pro události Redis do webového koncového bodu pomocí PowerShellu](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Rychlý Start: směrování mezipaměti Azure pro události Redis do webového koncového bodu pomocí Azure CLI](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).

