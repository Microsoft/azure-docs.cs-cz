---
title: Azure Application Insights Telemetrie datový Model - metriky Telemetrie | Dokumentace Microsoftu
description: Application Insights datový model pro metriky telemetrie
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: c365eea0465eab5fec134f270dab41e62cba2f1d
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811927"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Telemetrie metrik: Datový model Application Insights

Existují dva typy metrik telemetrických dat nepodporuje [Application Insights](../../application-insights/app-insights-overview.md): jeden měření a předem agregovaných metrik. Jedno měření je jenom název a hodnotu. Předem agregovaná metrika určuje minimální a maximální hodnota metriky v interval agregace a směrodatné odchylky poměru ho.

Předem agregovaných metrik telemetrie předpokládá tohoto období agregace byl jednu minutu.

Existuje několik známých názvy metrik podporované službou Application Insights. Tyto metriky se umístí do tabulky čítače výkonu.

Metrika představuje systém a proces čítače:

| **Název rozhraní .NET**             | **Agnostické název platformy** | **Název rozhraní REST API** | **Popis**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Probíhající práce... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | Celkový počet počítačů procesoru
| `\Memory\Available Bytes`                 | Probíhající práce... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Zobrazuje velikost fyzické paměti v bajtech, k dispozici pro procesy spuštěné v počítači. Počítá se sečtením množství místa v seznamech vynulované, bezplatným a pohotovostní paměti. Množství volné paměti je připravený k použití. vynulované paměti se skládá ze stránek paměti, které jsou vyplněny nulami zabráníte novější procesy zobrazovat data používaná předchozího procesu. pohotovostní paměti je paměť, která byla odebrána z pracovní sady procesu (jeho fyzické paměti) cestě na disk, ale je stále možné zrušit. Zobrazit [objektů paměti do mezipaměti](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Probíhající práce... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | Procesor procesu, který je hostitelem aplikace
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Probíhající práce... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | paměti využívané procesem, který je hostitelem aplikace
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Probíhající práce... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | Počet vstupně-výstupních operací spustí proces, který je hostitelem aplikace
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Probíhající práce... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | Počet požadavků zpracovaných aplikace 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Probíhající práce... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | počet výjimek vyvolaných aplikací
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Probíhající práce... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | Doba provádění průměrných požadavků
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Probíhající práce... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | Počet požadavků čekajících na zpracování ve frontě

## <a name="name"></a>Název

Název metriky, které byste chtěli vidět v portálu Application Insights a uživatelského rozhraní. 

## <a name="value"></a>Hodnota

Jednu hodnotu pro měření. Součet jednotlivých měření pro agregaci.

## <a name="count"></a>Počet

Váha metriky agregované metriky. By neměla být nastavena pro měření.

## <a name="min"></a>Minimum

Minimální hodnota agregovaná metrika. By neměla být nastavena pro měření.

## <a name="max"></a>Maximum

Maximální hodnota agregovaná metrika. By neměla být nastavena pro měření.

## <a name="standard-deviation"></a>Standardní odchylka

Směrodatná odchylka agregovaná metrika. By neměla být nastavena pro měření.

## <a name="custom-properties"></a>Vlastní vlastnosti

Vlastní vlastnost metrika `CustomPerfCounter` nastavena na `true` znamenat, že metrika představuje čítačů výkonu systému windows. Tyto metriky jsou umístěny v tabulce čítače výkonu. Není v customMetrics. Název této metriky je také analyzovat extrahovat kategorie, čítače a názvů instancí.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Další postup

- Další informace o použití [API pro Application Insights pro vlastní události a metriky](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Zobrazit [datový model](data-model.md) pro typy a datový model Application Insights.
- Podívejte se na [platformy](../../application-insights/app-insights-platforms.md) podporované službou Application Insights.
