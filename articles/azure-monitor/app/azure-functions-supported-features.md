---
title: Přehledy aplikací Azure – podporované funkce Azure
description: Podporované funkce přehledů aplikací pro funkce Azure
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cf0c97fd65f9966bf42fa22e2c8f92263952cb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655646"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Podporované funkce Application Insights for Azure Functions

Funkce Azure nabízí [integrovanou integraci](../../azure-functions/functions-monitoring.md) s Application Insights, která je dostupná prostřednictvím rozhraní ILogger. Níže je uveden seznam aktuálně podporovaných funkcí. Přečtěte si průvodce Funkce Azure pro [Začínáme](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

Další informace o verzích runtime funkce naleznete [zde](../../azure-functions/functions-versions.md).

Další informace o kompatibilních verzích Application Insights naleznete v tématu [Dependencies](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

## <a name="supported-features"></a>Podporované funkce

| Azure Functions                       | V1                | V2 & V3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **Automatický sběr**        |                 |                   |               
| &bull;Požadavky                     | Ano             | Ano               | 
| &bull;Výjimky                   | Ano             | Ano               | 
| &bull;Čítače výkonu         | Ano             | Ano               |
| &bull;Závislosti                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash;Protokol HTTP      |                 | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash;Sběrnice ServiceBus|                 | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash;EventHub  |                 | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash;Sql       |                 | Ano               | 
| | | | 
| **Podporované funkce**                |                   |                   |               
| &bull;QuickPulse/LiveMetrics       | Ano             | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash;Kanál zabezpečeného řízení|                 | Ano               | 
| &bull;Odběru vzorků                     | Ano             | Ano               | 
| &bull;Prezenční signály                   |                 | Ano               | 
| | | | 
| **Korelace**                       |                   |                   |               
| &bull;Sběrnice ServiceBus                     |                   | Ano               | 
| &bull;EventHub                       |                   | Ano               | 
| | | | 
| **Konfigurovatelné**                      |                   |                   |           
| &bull;Plně konfigurovatelné.<br/>Pokyny najdete v [tématu Funkce Azure.](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852)<br/>Všechny možnosti najdete [v tématu Asp.NET Jádro.](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration)               |                   | Ano                   | 


## <a name="performance-counters"></a>Čítače výkonu

Automatická kolekce čítačů výkonu funguje pouze pro počítače se systémem Windows.


## <a name="live-metrics--secure-control-channel"></a>Živé metriky & kanál s bezpečným řízením

Zadaná vlastní kritéria filtrů jsou odeslána zpět do komponenty Živé metriky v sadě SDK Application Insights. Filtry mohou potenciálně obsahovat citlivé informace, jako jsou id zákazníka. Kanál můžete zabezpečit pomocí tajného klíče rozhraní API. Pokyny naleznete [v tématu Zabezpečení řídicího kanálu.](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)

## <a name="sampling"></a>Vzorkování

Azure Functions umožňuje vzorkování ve výchozím nastavení v jejich konfiguraci. Další informace naleznete v [tématu Configure Sampling](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Pokud váš projekt trvá závislost na Application Insights SDK provádět ruční sledování telemetrie, může dojít k podivné chování, pokud vaše konfigurace vzorkování se liší od konfigurace vzorkování funkce. 

Doporučujeme používat stejnou konfiguraci jako funkce. S **funkcemi v2**můžete získat stejnou konfiguraci pomocí vkládání závislostí v konstruktoru:

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
