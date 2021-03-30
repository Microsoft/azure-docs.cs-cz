---
title: Vlastní schémata sledování pro zprávy B2B
description: Vytváření vlastních schémat sledování pro monitorování zpráv B2B v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "76903049"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Vytváření vlastních schémat sledování, která monitorují koncové pracovní postupy v Azure Logic A

Azure Logic Apps má integrované sledování, které můžete povolit pro části pracovního postupu. Můžete ale nastavit vlastní sledování, které protokoluje události od začátku až do konce pracovních postupů, například pracovní postupy, které obsahují aplikaci logiky, BizTalk Server, SQL Server nebo jakoukoli jinou vrstvu. Tento článek poskytuje vlastní kód, který můžete použít v vrstvách mimo vaši aplikaci logiky.

## <a name="custom-tracking-schema"></a>Vlastní schéma sledování

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": ,
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {}
      }
   ]
}
```

| Vlastnost | Požaduje se | Typ | Popis |
|----------|----------|------|-------------|
| Zdroje | Ano | Řetězec | Typ zdroje spuštění s těmito povolenými hodnotami: `Microsoft.Logic/workflows` , `custom` |
| source | Ano | Řetězec nebo JToken | Pokud se jedná o typ zdroje `Microsoft.Logic/workflows` , musí zdrojové informace postupovat podle tohoto schématu. Pokud je typ zdroje `custom` , je schéma JToken. |
| systemId | Ano | Řetězec | ID systému aplikace logiky |
| runId | Ano | Řetězec | ID spuštění aplikace logiky |
| operationName | Ano | Řetězec | Název operace, například akce nebo aktivační událost |
| repeatItemScopeName | Ano | Řetězec | Opakovat název položky, pokud je akce uvnitř `foreach` smyčky or `until` |
| repeatItemIndex | Ano | Integer | Označuje, že akce je uvnitř `foreach` smyčky nebo `until` a je opakujícím se číslem indexu položky. |
| trackingId | No | Řetězec | ID sledování ke korelaci zpráv |
| correlationId | No | Řetězec | ID korelace ke korelaci zpráv |
| ID žádosti klienta | No | Řetězec | Klient může tuto vlastnost naplnit tak, aby koreluje zprávy. |
| eventLevel | Ano | Řetězec | Úroveň události |
| eventTime | Ano | DateTime | Čas události ve formátu UTC: *RRRR-MM-DDThh: mm: ss. 00000Z* |
| recordType | Ano | Řetězec | Typ záznamu sledování s touto povolenou hodnotou: `custom` |
| zapisovací | Ano | JToken | Vlastní typ záznamu pouze ve formátu JToken |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování protokolu B2B

Informace o schématech sledování protokolu B2B najdete v těchto tématech:

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémata sledování X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Další kroky

* Další informace o [sledování zpráv B2B pomocí protokolů Azure monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)