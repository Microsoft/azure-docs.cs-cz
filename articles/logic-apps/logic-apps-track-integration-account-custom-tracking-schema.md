---
title: Vlastní schémata sledování pro zprávy B2B
description: Vytváření vlastních schémat sledování, která monitorují zprávy B2B v integračních účtech pro Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 7d7c5ef9e9a86c8b061a56fe41c0c8bbfc5ddbb3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792797"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Vytváření vlastních schémat sledování, která sledují koncové pracovní postupy v Azure Logic Apps

Existuje integrované sledování, které můžete povolit pro různé části pracovního postupu Business-to-Business, jako je například sledování zpráv AS2 nebo X12. Když vytváříte pracovní postupy, které obsahují aplikaci logiky, BizTalk Server, SQL Server nebo jakoukoli jinou vrstvu, můžete povolit vlastní sledování, které protokoluje události od začátku až do konce pracovního postupu. 

Tento článek poskytuje vlastní kód, který můžete použít v vrstvách mimo vaši aplikaci logiky. 

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
         "repeatItemIndex": "",
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
         "record": {                
         }
      }
   ]
}
```

| Vlastnost | Požaduje se | Typ | Popis |
| --- | --- | --- | --- |
| Zdroje | Ano |   | Typ zdroje spuštění. Povolené hodnoty jsou **Microsoft. Logic/Workflows** a **Custom**. |
| source | Ano |   | Pokud je typ zdroje **Microsoft. Logic/Workflows**, zdrojové informace musí postupovat podle tohoto schématu. Pokud je typ zdroje **vlastní**, je schéma JToken. |
| systemId | Ano | Řetězec | ID systému aplikace logiky |
| RunId | Ano | Řetězec | ID spuštění aplikace logiky |
| operationName | Ano | Řetězec | Název operace (například akce nebo Trigger). |
| repeatItemScopeName | Ano | Řetězec | Pokud je akce uvnitř `foreach`/`until` smyčka, opakovat název položky. |
| repeatItemIndex | Ano | Integer | Určuje, zda je akce uvnitř `foreach`/`until` smyčka. Označuje index opakované položky. |
| trackingId | Ne | Řetězec | ID sledování, aby bylo možné korelovat zprávy. |
| correlationId | Ne | Řetězec | ID korelace, aby bylo možné korelovat zprávy. |
| ID žádosti klienta | Ne | Řetězec | Klient je může naplnit tak, aby koreluje zprávy. |
| eventLevel | Ano |   | Úroveň události |
| Čas události | Ano |   | Čas události ve formátu UTC RRRR-MM-DDTHH: MM: SS. 00000Z. |
| RecordType | Ano |   | Typ záznamu sledování Povolená hodnota je **vlastní**. |
| zapisovací | Ano |   | Vlastní typ záznamu. Povolený formát je JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování protokolu B2B

Informace o schématech sledování protokolu B2B najdete v těchto tématech:

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schémata sledování X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Další kroky

* Další informace o [sledování zpráv B2B](logic-apps-monitor-b2b-message.md)
* Další informace o [sledování zpráv B2B v protokolech Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
