---
title: Vlastní sledování schémata pro monitorování B2B - Azure Logic Apps | Microsoft Docs
description: Vytvořte vlastní sledování schémata ke sledování zpráv B2B z transakcí ve vašem účtu integrace Azure.
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 94de6afcf3f9f95a3cb45207ef43e1d33b728d99
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="enable-tracking-to-monitor-your-complete-workflow-end-to-end"></a>Povolení sledování monitorování dokončení pracovního postupu, klient server
Je integrované sledování, že můžete povolit pro různé části pracovního postupu business-to-business, jako je například sledování AS2 nebo X12 zprávy. Při vytváření pracovních postupů, které obsahuje aplikace logiky, BizTalk Server, SQL Server nebo jinou vrstvu, pak můžete povolit vlastní sledování, který protokoluje události od začátku do konce pracovní postup. 

Toto téma obsahuje vlastní kód, který můžete použít v vrstvy mimo aplikaci logiky. 

## <a name="custom-tracking-schema"></a>Vlastní schéma sledování
````java

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

````

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| SourceType |   | Typ spuštění zdroje. Povolené hodnoty jsou **Microsoft.Logic/workflows** a **vlastní**. (Povinné) |
| Zdroj |   | Pokud je typ zdroje **Microsoft.Logic/workflows**, informace o zdroji musí postupujte podle tohoto schématu. Pokud je typ zdroje **vlastní**, schéma je JToken. (Povinné) |
| ID systému | Řetězec | ID logiku aplikace systému. (Povinné) |
| identifikátor runId | Řetězec | Aplikace logiky spustit ID. (Povinné) |
| operationName | Řetězec | Název operace (například akce nebo aktivační událost). (Povinné) |
| repeatItemScopeName | Řetězec | Název položky opakovat, pokud je akce uvnitř `foreach` / `until` smyčky. (Povinné) |
| repeatItemIndex | Integer | Jestli je akce uvnitř `foreach` / `until` smyčky. Určuje index opakovaných položky. (Povinné) |
| trackingId | Řetězec | ID sledování ke korelaci zprávy. (Volitelné) |
| correlationId | Řetězec | ID korelace ke korelaci zprávy. (Volitelné) |
| clientRequestId | Řetězec | Klienta můžete naplnit ji ke korelaci zprávy. (Volitelné) |
| eventLevel |   | Úroveň události. (Povinné) |
| eventTime |   | Čas události ve formátu RRRR-MM-DDTHH:MM:SS.00000Z UTC. (Povinné) |
| recordType |   | Typ sledování záznamu. Povolená hodnota je **vlastní**. (Povinné) |
| záznam |   | Vlastní typ záznamu. Povolený formát je JToken. (Povinné) |

## <a name="b2b-protocol-tracking-schemas"></a>Schémata sledování protokol B2B
Informace o protokolu B2B sledování schémata najdete v tématu:
* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schémata sledování X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Další postup
* Další informace o [sledování zpráv B2B](logic-apps-monitor-b2b-message.md).   
* Další informace o [sledování zpráv B2B ve analýzy protokolů](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Další informace o [Enterprise integračního balíčku](../logic-apps/logic-apps-enterprise-integration-overview.md).
