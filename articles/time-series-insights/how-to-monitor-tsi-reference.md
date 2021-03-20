---
title: Monitorování referenčních dat Azure Time Series Insights | Microsoft Docs
description: Referenční dokumentace k monitorování Azure Time Series Insights.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: bfd0d04313f0b519b4013a43e29d88400c73ea31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591377"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Referenční informace o Azure Time Series Insights dat monitorování

Seznamte se s daty a prostředky shromažďovanými nástrojem Azure Monitor ze Azure Time Series Insights prostředí. Podrobnosti o shromažďování a analýze dat monitorování najdete v tématu [monitorování Time Series Insights]( ./how-to-monitor-tsi.md) .

## <a name="metrics"></a>Metriky

V této části jsou uvedené všechny automaticky shromážděné metriky platforem pro Azure Time Series Insights. Seznam všech metrik podpory Azure Monitor (včetně Azure Time Series Insights) najdete v tématu [Azure monitor podporované metriky](../azure-monitor/essentials/metrics-supported.md). Poskytovatel prostředků pro tyto metriky je [Microsoft. TimeSeriesInsights/Environment/EventSources](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) a [Microsoft. TimeSeriesInsights/Environments](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironments).


### <a name="ingress"></a>Příchozí přenos dat
 
|Metric|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|
|---|---|---|---|---|
|IngressReceivedBytes|Přijaté bajty příchozího přenosu dat|Bajty|Celkem|Počet přečtených bajtů ze zdroje události|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijal neplatné zprávy.|Počet|Celkem|Počet neplatných zpráv přečtených ze zdroje události|
|IngressReceivedMessages|Příchozí zprávy příchozího přenosu dat|Počet|Celkem|Počet zpráv přečtených ze zdroje události|
|IngressReceivedMessagesCountLag|Prodleva počtu přijatých zpráv příchozího přenosu|Počet|Průměr|Rozdíl mezi pořadovým číslem poslední zprávy ve frontě ve zdrojovém oddílu události a pořadovým číslem zpracovávaných zpráv v příchozím přenosu dat|
|IngressReceivedMessagesTimeLag|Časová prodleva přijatých příchozích zpráv příchozího přenosu dat|Sekundy|Maximum|Rozdíl mezi časem, kdy je zpráva zařazená do fronty ve zdroji událostí a čas zpracování v příchozím přenosu dat|
|IngressStoredBytes|Uložené bajty příchozího přenosu dat|Bajty|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|
|IngressStoredEvents|Uložené události příchozího přenosu dat|Počet|Celkem|Počet úspěšně zpracovaných a dostupných dotazů v počtu plochých událostí|

### <a name="storage"></a>Storage

|Metric|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|
|---|---|---|---|---|
|WarmStorageMaxProperties|Maximální počet vlastností služby teplé úložiště|Počet|Maximum|Maximální počet vlastností používaných prostředím pro SKU S1/S2 a maximální počet vlastností povolený pro PAYG SKU v rámci služby teplé úložiště|
|WarmStorageUsedProperties|Vlastnosti použití teplého úložiště |Počet|Maximum|Počet vlastností používaných prostředím pro SKU S1/S2 a počet vlastností, které používá služba pro zahřívání v úložišti pro PAYG|

## <a name="resource-logs"></a>Protokoly prostředků

V této části jsou uvedené typy protokolů prostředků, které můžete shromažďovat pro Azure Time Series Insights prostředí.

| Kategorie | Zobrazovaný název | Popis |
|----- |----- |----- |
| Příchozí přenos dat | TSIIngress | Kategorie příchozího přenosu dat sleduje chyby, ke kterým došlo v kanálu příchozího přenosu dat. Tato kategorie zahrnuje chyby, ke kterým dochází při přijímání událostí (například selhání pro připojení ke zdroji událostí) a zpracování událostí (například chyby při analýze datové části události). |

## <a name="schemas"></a>Schémata
Následující schémata používá Azure Time Series Insights

### <a name="tsiingress-table"></a>Tabulka TSIIngress

| Vlastnost | Popis |
|----- |----- |
| TimeGenerated | Čas (UTC), kdy se tato událost vygeneruje |
| Umístění | Umístění prostředku. |
| Kategorie | Kategorie události protokolu |
| OperationName | Název operace události |
| CorrelationId | ID korelace požadavku. |
| Level | Úroveň závažnosti události |
| ResultDescription | Popis výsledku operace, jako je například přijatá chyba přijetí zákazu. |
| Zpráva | Zpráva přidružená k chybě Obsahuje podrobné informace o tom, co se stalo, a o tom, jak tuto chybu zmírnit. |
| ErrorCode | Kód přidružený k chybě |
| EventSourceType | Typ zdroje události Může to být buď centrum událostí, nebo centrum IoT. |
| EventSourceProperties | Kolekce vlastností, které jsou specifické pro váš zdroj události. Obsahuje podrobnosti, jako je například skupina příjemců a název přístupového klíče. |
