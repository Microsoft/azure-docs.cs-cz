---
title: Kódy chyb externích dostupnosti – Azure Stream Analytics
description: Řešení potíží s Azure Stream Analytics problémy s kódy chyb externích dostupnosti.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 092b548e73330fe80ef10b2e25d9ab7883b256ac
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597790"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Azure Stream Analytics kódů chyb externích dostupnosti

Protokoly aktivit a protokoly prostředků můžete použít k ladění neočekávaného chování z vaší Azure Stream Analytics úlohy. V tomto článku je uveden popis všech kódů chyb externí dostupnosti. K externím chybám dostupnosti dojde, když je závislá služba nedostupná.

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **Příčina**: služba je dočasně nedostupná.
* **Doporučení**: Stream Analytics se bude i nadále pokoušet o přístup ke službě. 

## <a name="kafkaservernotavailable"></a>KafkaServerNotAvailable

* **Příčina**: Server Kafka není k dispozici.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **Příčina**: Stream Analytics došlo k chybě při komunikaci s EventHub. 


## <a name="next-steps"></a>Další kroky

* [Řešení potíží se vstupními připojeními](stream-analytics-troubleshoot-input.md)
* [Řešení potíží s výstupy Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Řešení potíží s Azure Stream Analytics dotazy](stream-analytics-troubleshoot-query.md)
* [Řešení potíží s Azure Stream Analytics pomocí protokolů prostředků](stream-analytics-job-diagnostic-logs.md)
* [Chyby Azure Stream Analytics dat](data-errors.md)
