---
title: Datový Model aplikace Azure Statistika Telemetrie - metriky Telemetrie | Microsoft Docs
description: Application Insights datový model pro metriky telemetrie
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: 3feb1e79ba4e4666cbe9ffc77136520922728710
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="metric-telemetry-application-insights-data-model"></a>Metriky telemetrie: Application Insights datový model

Existují dva typy metriky telemetrie nepodporuje [Application Insights](app-insights-overview.md): jeden měření a předem agregovaná metrika. Jedno měření je právě název a hodnotu. Předem agregovaná metrika určuje minimální a maximální hodnotou metriky v jako interval agregace a směrodatné odchylky ho.

Předem agregované metriky telemetrie předpokládá toto období agregace byl jednu minutu.

Existuje několik dobře známé metriky jmen nepodporuje Application Insights. Tyto metriky umístit do tabulky čítače výkonu.

Metrika představující systém a proces čítače:

| **Název rozhraní .NET**             | **Název lhostejné platformy** | **Název REST API** | **Popis**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Probíhající práce... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | Celkový počet počítačů procesoru
| `\Memory\Available Bytes`                 | Probíhající práce... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Zobrazuje velikost fyzické paměti, v bajtech, k dispozici pro procesy spuštěné v počítači. Vypočítá se jako součet množství místa v seznamech vynulované, volných a paměti. Množství volné paměti je připravený k použití; vynulované paměti se skládá z stránek paměti vyplněno nulami na novější procesy zabránit v zobrazení dat používá předchozí proces; pohotovostní paměť je paměti, který byl odebrán z pracovní sady procesu (jeho fyzické paměti) během cesty k disku, ale je třeba připomenout, stále k dispozici. V tématu [paměti objektu](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Probíhající práce... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | Procesor procesu hostování aplikace
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Probíhající práce... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | využité procesem hostování aplikace
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Probíhající práce... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | Počet vstupně-výstupních operací se spustí proces hostování aplikace
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Probíhající práce... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | Míra požadavků zpracovaných aplikací 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Probíhající práce... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | počet výjimek vyvolaných aplikace
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Probíhající práce... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | čas spuštění průměrný počet požadavků
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Probíhající práce... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | Počet požadavků čekajících na zpracování ve frontě

## <a name="name"></a>Název

Název metriky, kterou jste chtěli vidět v portálu Application Insights a uživatelského rozhraní. 

## <a name="value"></a>Hodnota

Jednu hodnotu pro měření. Součet jednotlivými měřeními pro agregaci.

## <a name="count"></a>Počet

Váha metriky agregovaná metrika. Neměla by být nastavená pro měření.

## <a name="min"></a>Min.

Minimální hodnota agregovaná metrika. Neměla by být nastavená pro měření.

## <a name="max"></a>Max.

Maximální hodnota agregovaná metrika. Neměla by být nastavená pro měření.

## <a name="standard-deviation"></a>Směrodatná odchylka

Standardní odchylka agregovaná metrika. Neměla by být nastavená pro měření.

## <a name="custom-properties"></a>Vlastní vlastnosti

Metrika s vlastní vlastnost `CustomPerfCounter` nastavena na `true` označení, že metriku představuje čítačů výkonu systému windows. Tyto metriky umístěny v tabulce čítače výkonu. Není v customMetrics. Název tato metrika je také analyzovat extrahovat kategorie čítače a názvy instancí.

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Další postup

- Další informace o použití [Application Insights API pro vlastní události a metriky](app-insights-api-custom-events-metrics.md#trackmetric).
- V tématu [datový model](application-insights-data-model.md) Application Insights typy a data modelu.
- Podívejte se na [platformy](app-insights-platforms.md) nepodporuje Application Insights.
