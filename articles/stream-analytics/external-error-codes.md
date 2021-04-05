---
title: Kódy externích chyb – Azure Stream Analytics
description: Řešení potíží s Azure Stream Analytics problémy s externími kódy chyb.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9f55a715b11b126ea340e665e008d7245e578190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016384"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Azure Stream Analytics kódů externích chyb

Protokoly aktivit a protokoly prostředků můžete použít k ladění neočekávaného chování z vaší Azure Stream Analytics úlohy. V tomto článku je uveden popis každého externího kódu chyby. Externí chyby jsou obecné chyby vyvolané službou pro odesílání nebo příjem dat, které Stream Analytics nemůžou rozlišovat jako chybu dat, chybu konfigurace nebo chybu externí dostupnosti.

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **Příčina**: při inicializaci adaptéru došlo k chybě.

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **Příčina**: při zápisu dat na adaptér došlo k chybě.

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **Příčina**: Služba Azure Functions vrátila chybu HTTP.

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **Příčina**: Stream Analytics se nepovedlo zapsat události do funkce Azure Functions.

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **Příčina**: při vkládání do Azure Functions došlo k chybě přesměrování.

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **Příčina**: došlo k chybě klienta při výstupu Azure Functions.

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **Příčina**: došlo k chybě serveru, která se zapisuje do Azure Functions.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **Příčina**: zápis do služby Azure Functions se nezdařil, protože požadavek HTTP překročil časový limit. 
* **Doporučení**: případné prodlevy najdete v protokolech Azure Functions.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **Příčina**: posuny vstupu jsou neplatné. To může být způsobeno převzetím služeb při selhání.
* **Doporučení**: restartujte úlohu Stream Analytics z času posledního výstupu.

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **Příčina**: při odesílání dat do centra událostí došlo k chybě.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **Příčina**: po maximálním počtu opakovaných pokusů se Stream Analytics nepodařilo připojit k účtu Cosmos DB.

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **Příčina**: Stream Analytics se nepodařilo zadat dotaz na databázi Cosmos DB a kolekci po maximálním počtu opakovaných pokusů.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **Příčina**: CosmosDB nemůže vytvořit uloženou proceduru po několika opakovaných pokusech.

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **Příčina**: uložená procedura Upsert vrátila chybu. 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **Příčina**: Stream Analytics nemůže inicializovat výstup SQL Database.

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **Příčina**: Stream Analytics nemůže zapisovat události do výstupu SQL Database.

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **Příčina**: při inicializaci vyhrazeného výstupu fondu SQL došlo k chybě.

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **Příčina**: při zápisu výstupu do vyhrazeného fondu SQL došlo k chybě.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží se vstupními připojeními](stream-analytics-troubleshoot-input.md)
* [Řešení potíží s výstupy Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Řešení potíží s Azure Stream Analytics dotazy](stream-analytics-troubleshoot-query.md)
* [Řešení potíží s Azure Stream Analytics pomocí protokolů prostředků](stream-analytics-job-diagnostic-logs.md)
* [Chyby Azure Stream Analytics dat](data-errors.md)
