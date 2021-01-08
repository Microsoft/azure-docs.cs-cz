---
title: Kódy chyb dat – Azure Stream Analytics
description: Řešení potíží s Azure Stream Analyticsmi kódy chyb dat.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 33e617c8c3589d76d649dd2ea2236a4247b12500
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016486"
---
# <a name="azure-stream-analytics-data-error-codes"></a>Kódy chyb Azure Stream Analytics dat

Protokoly aktivit a protokoly prostředků můžete použít k ladění neočekávaného chování z vaší Azure Stream Analytics úlohy. V tomto článku je uveden popis každého kódu chyby chyby dat. K chybám dat dochází v případě, že datový proud obsahuje chybná data, jako je například neočekávané schéma záznamu.

## <a name="inputdeserializationerror"></a>InputDeserializationError

* **Příčina**: Při deserializaci vstupních dat došlo k chybě.

## <a name="inputeventtimestampnotfound"></a>InputEventTimestampNotFound

* **Příčina**: Stream Analytics nemůže získat časové razítko pro prostředek. 

## <a name="inputeventtimestampbyovervaluenotfound"></a>InputEventTimestampByOverValueNotFound

* **Příčina**: Stream Analytics nemůže získat hodnotu `TIMESTAMP BY OVER COLUMN` .

## <a name="inputeventlatebeyondthreshold"></a>InputEventLateBeyondThreshold

* **Příčina**: událost vstupu byla odeslána později než nakonfigurovaná tolerance.

## <a name="inputeventearlybeyondthreshold"></a>InputEventEarlyBeyondThreshold

* **Příčina**: doba doručení události vstupu je starší než prahová hodnota časového razítka aplikace vstupní události.

## <a name="azurefunctionmessagesizeexceeded"></a>AzureFunctionMessageSizeExceeded

* **Příčina**: výstup zprávy do Azure Functions překračuje limit velikosti.

## <a name="eventhuboutputrecordexceedssizelimit"></a>EventHubOutputRecordExceedsSizeLimit

* **Příčina**: výstupní záznam překračuje limit maximální velikosti při zápisu do centra událostí.

## <a name="cosmosdboutputinvalidid"></a>CosmosDBOutputInvalidId

* **Příčina**: hodnota nebo typ konkrétního sloupce je neplatný.
* **Doporučení**: Zadejte jedinečné neprázdné řetězce, které nejsou delší než 255 znaků.

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **Příčina**: ID dokumentu výstupního záznamu obsahuje neplatný znak.

## <a name="cosmosdboutputmissingid"></a>CosmosDBOutputMissingId

* **Příčina**: výstupní záznam neobsahuje \[ ID sloupce], které by bylo možné použít jako vlastnost primárního klíče.

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **Příčina**: výstupní záznam neobsahuje vlastnost ID dokumentu. 
* **Doporučení**: Ujistěte se, že výstup dotazu obsahuje sloupec s jedinečným neprázdným řetězcem, který je kratší než 255 znaků.

## <a name="cosmosdboutputmissingpartitionkey"></a>CosmosDBOutputMissingPartitionKey

* **Příčina**: ve výstupním záznamu chybí sloupec, který se má použít jako vlastnost klíče oddílu.

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **Příčina**: jeden záznam pro zápis do Cosmos DB je příliš velký.

## <a name="sqldatabaseoutputdataerror"></a>SQLDatabaseOutputDataError

* **Příčina**: Stream Analytics nemůže zapisovat události, které se mají SQL Database kvůli problémům s daty.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží se vstupními připojeními](stream-analytics-troubleshoot-input.md)
* [Řešení potíží s výstupy Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Řešení potíží s Azure Stream Analytics dotazy](stream-analytics-troubleshoot-query.md)
* [Řešení potíží s Azure Stream Analytics pomocí protokolů prostředků](stream-analytics-job-diagnostic-logs.md)
* [Chyby Azure Stream Analytics dat](data-errors.md)
