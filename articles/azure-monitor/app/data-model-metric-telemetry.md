---
title: Datový model pro telemetrii metrik – Azure Application Insights
description: Application Insights datový model pro telemetrii metrik
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: f8e787b8a94987e7d68e12a0e4e69f4c63e21e19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320574"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Telemetrie metrik: datový model Application Insights

[Application Insights](./app-insights-overview.md)podporují dva typy telemetrie metrik: jedno měření a předem agregovaná metrika. Jedno měření je pouze název a hodnota. Předem agregovaná metrika určuje minimální a maximální hodnotu metriky v intervalu agregace a směrodatnou odchylku.

Předběžně agregovaná telemetrie metriky předpokládá, že je agregované období jedna minuta.

Application Insights podporuje několik dobře známých názvů metrik. Tyto metriky jsou umístěné do tabulky čítače výkonu.

Metrika představující systémové a procesní čítače:

| **Název .NET**             | **Název platformy nezávislá** | **Název REST API** | **Popis**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Probíhá práce... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | Celkový procesor počítače
| `\Memory\Available Bytes`                 | Probíhá práce... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Zobrazuje velikost fyzické paměti (v bajtech), která je k dispozici pro procesy běžící v počítači. Vypočítá se tak, že se sečte velikost místa v seznamech neplatných, volných a hotovostních seznamů paměti. Volná paměť je připravena k použití. s nulovou pamětí se skládají stránky z paměti vyplněné nulami, aby se předešlo pozdějším procesům v zobrazení dat využívaných předchozím procesem. pohotovostní paměť je paměť, která byla odstraněna z pracovní sady procesu (její fyzická paměť) na disk, ale je stále k dispozici pro odvolání. Zobrazit [objekt paměti](/previous-versions/ms804008(v=msdn.10))
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Probíhá práce... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | PROCESOR procesu hostujícího aplikaci
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Probíhá práce... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | paměť používaná procesem hostujícím aplikaci
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Probíhá práce... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | rychlost vstupně-výstupních operací spouštěných procesem hostujícím aplikaci
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Probíhá práce... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | frekvence požadavků zpracovaných aplikací 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Probíhá práce... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | frekvence výjimek vyvolaných aplikací
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Probíhá práce... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | Průměrná doba provádění požadavků
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Probíhá práce... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | počet požadavků čekajících na zpracování ve frontě

## <a name="name"></a>Name

Název metriky, kterou byste chtěli vidět na portálu Application Insights a uživatelském rozhraní. 

## <a name="value"></a>Hodnota

Jedna hodnota pro měření. Součet jednotlivých měření pro agregaci.

## <a name="count"></a>Počet

Váha metriky agregované metriky. Neměl by být nastaven pro měření.

## <a name="min"></a>Min

Minimální hodnota agregované metriky Neměl by být nastaven pro měření.

## <a name="max"></a>Maximum

Maximální hodnota agregované metriky Neměl by být nastaven pro měření.

## <a name="standard-deviation"></a>Směrodatná odchylka

Směrodatná odchylka agregované metriky. Neměl by být nastaven pro měření.

## <a name="custom-properties"></a>Vlastní vlastnosti

Metrika s `CustomPerfCounter` nastavenou vlastní vlastností `true` , která označuje, že metrika představuje čítač výkonu systému Windows. Tyto metriky jsou umístěné v tabulce čítače výkonu. Není v customMetrics. Název této metriky se také analyzuje za účelem extrakce názvů kategorií, čítačů a instancí.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Další kroky

- Naučte se používat [rozhraní Application Insights API pro vlastní události a metriky](./api-custom-events-metrics.md#trackmetric).
- Viz [datový model](data-model.md) pro typy Application Insights a datový model.
- Podívejte se na [platformy](./platforms.md) podporované nástrojem Application Insights.

