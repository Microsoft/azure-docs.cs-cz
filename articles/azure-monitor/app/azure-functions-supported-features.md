---
title: Funkce podporované službou Azure Application Insights – Azure Functions
description: Application Insights podporované funkce pro Azure Functions
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 333bba2b1d3cd83457196e38b827daa78199f235
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87033509"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights pro podporované funkce Azure Functions

Azure Functions nabízí [integrovanou integraci](../../azure-functions/functions-monitoring.md) s Application Insights, která je k dispozici prostřednictvím rozhraní ILogger. Níže je uveden seznam aktuálně podporovaných funkcí. Projděte si Azure Functions příručka [Začínáme](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

Další informace o verzích modulu runtime Functions najdete [zde](../../azure-functions/functions-versions.md).

Další informace o kompatibilních verzích Application Insights najdete v tématu [závislosti](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

## <a name="supported-features"></a>Podporované funkce

| Azure Functions                       | V1                | V2 & V3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **Automatická kolekce**        |                 |                   |               
| &bull; Požádal                     | Yes             | Yes               | 
| &bull; Výjimek                   | Yes             | Yes               | 
| &bull; Čítače výkonu         | Yes             | Yes               |
| &bull; Závislosti                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Yes               | 
| | | | 
| **Podporované funkce**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Yes             | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; Kanál zabezpečeného řízení|                 | Yes               | 
| &bull; Kontrol                     | Yes             | Yes               | 
| &bull; Prezenčních signálů                   |                 | Yes               | 
| | | | 
| **Korelace**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Yes               | 
| &bull; EventHub                       |                   | Yes               | 
| | | | 
| **Konfigurovatelné**                      |                   |                   |           
| &bull;Plně konfigurovatelné.<br/>Pokyny najdete v tématu [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) .<br/>Všechny možnosti najdete v části [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) .               |                   | Yes                   | 


## <a name="performance-counters"></a>Čítače výkonu

Automatická kolekce čítačů výkonu funguje pouze v počítačích se systémem Windows.


## <a name="live-metrics--secure-control-channel"></a>Aktivní metriky & kanál zabezpečeného řízení

Vlastní kritéria filtrů, která zadáte, se vrátí zpět na komponentu živých metrik v sadě Application Insights SDK. Filtry mohou potenciálně obsahovat citlivé informace, jako jsou například KódZákazníka. Kanál můžete nastavit jako zabezpečený pomocí tajného klíče rozhraní API. Pokyny najdete v tématu [zabezpečení řídicího kanálu](./live-stream.md#secure-the-control-channel) .

## <a name="sampling"></a>Vzorkování

Azure Functions povolí vzorkování ve výchozím nastavení v konfiguraci. Další informace najdete v tématu [Konfigurace vzorkování](../../azure-functions/functions-monitoring.md#configure-sampling).

Pokud váš projekt získá závislost na sadě Application Insights SDK a provede ruční sledování telemetrie, může docházet k podivnému chování, pokud se konfigurace vzorkování liší od konfigurace vzorkování funkcí. 

Doporučujeme použít stejnou konfiguraci jako funkce. Se službami **Functions v2**můžete získat stejnou konfiguraci pomocí injektáže závislosti v konstruktoru:

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
