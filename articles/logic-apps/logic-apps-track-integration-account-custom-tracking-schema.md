---
title: Vlastní schémata sledování pro zprávy B2B
description: Vytvoření vlastních schémat sledování pro monitorování zpráv B2B v aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903049"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Vytvoření vlastních schémat sledování, která monitorují komplexní pracovní postupy v Azure Logic A

Azure Logic Apps má integrované sledování, které můžete povolit pro části pracovního postupu. Můžete však nastavit vlastní sledování, které zaznamenává události od začátku do konce pracovních postupů, například pracovní postupy, které zahrnují aplikaci logiky, BizTalk server, SQL Server nebo jakoukoli jinou vrstvu. Tento článek obsahuje vlastní kód, který můžete použít ve vrstvách mimo aplikaci logiky.

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
| Sourcetype | Ano | Řetězec | Typ zdroje spuštění s těmito `Microsoft.Logic/workflows`povolenými hodnotami: ,`custom` |
| source | Ano | Řetězec nebo JToken | Pokud je `Microsoft.Logic/workflows`typ zdroje , informace o zdroji musí následovat toto schéma. Pokud je `custom`typ zdroje , schéma je JToken. |
| systemId | Ano | Řetězec | ID systému aplikace logiky |
| runId | Ano | Řetězec | ID spuštění aplikace logiky |
| operationName | Ano | Řetězec | Název operace, například akce nebo aktivační událost |
| funkce repeatItemScopeName | Ano | Řetězec | Opakování názvu položky, pokud `foreach` `until` je akce uvnitř smyčky nebo |
| repeatItemIndex | Ano | Integer | Označuje, že akce `foreach` je `until` uvnitř smyčky nebo a je číslo indexu opakované položky. |
| trackingId | Ne | Řetězec | Sledování ID pro korelaci zpráv |
| correlationId | Ne | Řetězec | ID korelace ke korelaci zpráv |
| klientRequestId | Ne | Řetězec | Klient může naplnit tuto vlastnost ke korelaci zpráv |
| eventLevel | Ano | Řetězec | Úroveň akce |
| eventTime | Ano | DateTime | Čas události ve formátu UTC: *YYYY-MM-DDTHH:MM:SS.00000Z* |
| recordType | Ano | Řetězec | Typ záznamu s touto povolenou hodnotou:`custom` |
| Záznam | Ano | JToken | Vlastní typ záznamu pouze s formátem JToken |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování protokolu B2B

Informace o schématech sledování protokolu B2B naleznete v tématu:

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémata sledování X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Další kroky

* Další informace o [monitorování zpráv B2B pomocí protokolů Azure Monitoru](../logic-apps/monitor-b2b-messages-log-analytics.md)