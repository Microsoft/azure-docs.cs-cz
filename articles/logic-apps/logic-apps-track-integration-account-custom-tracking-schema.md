---
title: Vlastní sledování schémata na zprávy B2B – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvořit vlastní sledování schémata, které monitorování zpráv B2B v integračních účtů pro Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.date: 01/27/2017
ms.openlocfilehash: 68c5d6e68562d4027c102e1bde42c775648e58c4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124839"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Vytvořit vlastní sledování schémata, které sledují pracovní postupy začátku do konce v Azure Logic Apps

Je integrované sledování, můžete povolit pro různé části pracovního postupu business-to-business, jako je sledování AS2 nebo X12 zprávy. Při vytváření pracovních postupů obsahuje aplikaci logiky, BizTalk Server, SQL Server nebo jiné vrstvy, pak můžete povolit vlastní sledování, která protokoluje události od začátku do konce pracovního postupu. 

Tento článek obsahuje vlastní kód, který můžete použít v jednotlivých vrstvách mimo svou aplikaci logiky. 

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

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| sourceType |   | Typ spuštění zdroje. Povolené hodnoty jsou **Microsoft.Logic/workflows** a **vlastní**. (Povinné) |
| Zdroj |   | Pokud je typ zdrojového **Microsoft.Logic/workflows**, informace o zdroji je potřeba postupovat podle tohoto schématu. Pokud je typ zdrojového **vlastní**, schéma je JToken. (Povinné) |
| ID systému | Řetězec | ID logiku aplikace systému. (Povinné) |
| runId | Řetězec | ID spuštění aplikace logiky (Povinné) |
| operationName | Řetězec | Název operace (například akce nebo triggeru). (Povinné) |
| repeatItemScopeName | Řetězec | Pokud je akce uvnitř opakujte název položky `foreach` / `until` smyčky. (Povinné) |
| repeatItemIndex | Integer | Zda "action" je uvnitř `foreach` / `until` smyčky. Určuje index opakované položky. (Povinné) |
| trackingId | Řetězec | ID sledování ke korelaci zprávy. (Volitelné) |
| correlationId | Řetězec | ID korelace ke korelaci zprávy. (Volitelné) |
| ID žádosti klienta | Řetězec | Klienta můžete naplnit ho ke korelaci zprávy. (Volitelné) |
| eventLevel |   | Úroveň události. (Povinné) |
| čas události |   | Čas události ve formátu RRRR-MM-DDTHH:MM:SS.00000Z UTC. (Povinné) |
| Typ záznamu |   | Typ záznamu sledování. Povolená hodnota je **vlastní**. (Povinné) |
| záznam |   | Vlastní typ záznamu. Povolený formát je JToken. (Povinné) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování B2B protokolu

Informace o protokolu B2B schémata sledování najdete tady:

* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schémata sledování X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Další postup

* Další informace o [sledování zpráv B2B](logic-apps-monitor-b2b-message.md)
* Další informace o [sledování zpráv B2B v Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)