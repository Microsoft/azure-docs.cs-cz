---
title: Datový model pro metrickou telemetrii – Přehledy aplikací Azure
description: Datový model Application Insights pro metrickou telemetrii
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 3e4a1fc3de58b8e65ab9c7a288bdf3eb37e7bae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671966"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Metrice telemetrie: Datový model Application Insights

Existují dva typy metrických telemetrických dat podporovaných [Application Insights:](../../azure-monitor/app/app-insights-overview.md)jedno měření a předem agregovaná metrika. Jediné měření je pouze název a hodnota. Předem agregovaná metrika určuje minimální a maximální hodnotu metriky v intervalu agregace a její směrodatné odchylce.

Preagregovaná metrická telemetrie předpokládá, že období agregace byla jedna minuta.

Existuje několik známých názvů metrik podporovaných Application Insights. Tyto metriky umístěné do tabulky performanceCounters.

Metrika představující čítače systému a procesů:

| **Název rozhraní .NET**             | **Agnostik platformy jméno** | **Název rozhraní REST API** | **Popis**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Nedokončená práce... | [procesorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | celkový procesor počítače
| `\Memory\Available Bytes`                 | Nedokončená práce... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Zobrazuje množství fyzické paměti v bajtech, které jsou k dispozici pro procesy spuštěné v počítači. Vypočítá se sečtením množství místa v seznamu vynulovaných, volných a pohotovostních paměťových seznamů. Volná paměť je připravena k použití; nulová paměť se skládá ze stránek paměti vyplněných nulami, aby se zabránilo pozdějším procesům v zobrazení dat používaných předchozím procesem; Pohotovostní paměť je paměť, která byla odebrána z pracovní sady procesu (jeho fyzické paměti) na cestě na disk, ale je stále k dispozici pro odvolání. Viz [Objekt paměti](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Nedokončená práce... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU procesu hostujícího aplikaci
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Nedokončená práce... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | paměť používanou procesem hostujícím aplikaci
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Nedokončená práce... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | rychlost vstupně-va/v., probíhá podle procesu hostujícího aplikaci
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Nedokončená práce... | [požadavkyPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | míra žádostí zpracovávaných žádostí 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Nedokončená práce... | [výjimkyPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | míra výjimek vyháněných podle aplikace
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Nedokončená práce... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | průměrná doba provádění požadavků
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Nedokončená práce... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | počet požadavků čekajících na zpracování ve frontě

## <a name="name"></a>Name (Název)

Název metriky, kterou chcete zobrazit na portálu Application Insights a uživatelském uživatelském nastavení. 

## <a name="value"></a>Hodnota

Jednotná hodnota pro měření. Součet jednotlivých měření pro agregaci.

## <a name="count"></a>Počet

Metrická hmotnost agregované metriky. By nemělbýt nastaven pro měření.

## <a name="min"></a>Minimum

Minimální hodnota agregované metriky. By nemělbýt nastaven pro měření.

## <a name="max"></a>Maximum

Maximální hodnota agregované metriky. By nemělbýt nastaven pro měření.

## <a name="standard-deviation"></a>Směrodatná odchylka

Směrodatná odchylka agregované metriky. By nemělbýt nastaven pro měření.

## <a name="custom-properties"></a>Vlastní vlastnosti

Metrika s `CustomPerfCounter` vlastní `true` vlastnosti nastavit označující, že metrika představuje čítač výkonu systému Windows. Tyto metriky umístěné v tabulce performanceCounters. Není v customMetrics. Také název této metriky je analyzován extrahovat kategorie, čítač a názvy instancí.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak používat [rozhraní API Application Insights pro vlastní události a metriky](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Viz [datový model](data-model.md) pro typy application insights a datový model.
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované Application Insights.
