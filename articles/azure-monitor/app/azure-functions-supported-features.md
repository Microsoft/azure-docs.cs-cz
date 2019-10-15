---
title: Azure Application Insights – podporované funkce Azure Functions | Microsoft Docs
description: Application Insights podporované funkce pro Azure Functions
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 46e7f5b5376622be9a05265942b6aceb38feebac
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372443"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights pro podporované funkce Azure Functions

Azure Functions nabízí [integrovanou integraci](../../azure-functions/functions-monitoring.md) s Application Insights, která je k dispozici prostřednictvím rozhraní ILogger. Níže je uveden seznam aktuálně podporovaných funkcí. Projděte si Azure Functions příručka [Začínáme](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

## <a name="supported-features"></a>Podporované funkce

| Funkce Azure                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Sada Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Automatická kolekce**        |                 |                   |               
| @no__t – 0 požadavků                     | Ano             | Ano               | 
| Výjimky &bull;                   | Ano             | Ano               | 
| Čítače výkonu &bull;         | Ano             | Ano               |
| @no__t – 0 závislostí                   |                   |                   |               
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 HTTP      |                 | Ano               | 
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 ServiceBus|                 | Ano               | 
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 EventHub  |                 | Ano               | 
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 SQL       |                 | Ano               | 
| | | | 
| **Podporované funkce**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Ano             | Ano               | 
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 zabezpečený řídicí kanál|                 | Ano               | 
| Vzorkování &bull;                     | Ano             | Ano               | 
| @no__t – 0 prezenčních signálů                   |                 | Ano               | 
| | | | 
| **Korelace**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Ano               | 
| @no__t – 0 EventHub                       |                   | Ano               | 
| | | | 
| **Konfigurovatelné**                      |                   |                   |           
| @no__t – 0Fully konfigurovatelné.<br/>Pokyny najdete v tématu [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) .<br/>Všechny možnosti najdete v části [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) .               |                   | Ano                   | 


## <a name="performance-counters"></a>Čítače výkonu

Automatická kolekce čítačů výkonu funguje pouze v počítačích se systémem Windows.


## <a name="live-metrics--secure-control-channel"></a>Aktivní metriky & kanál zabezpečeného řízení

Vlastní kritéria filtrů, která zadáte, se vrátí zpět na komponentu živých metrik v sadě Application Insights SDK. Filtry mohou potenciálně obsahovat citlivé informace, jako jsou například KódZákazníka. Kanál můžete nastavit jako zabezpečený pomocí tajného klíče rozhraní API. Pokyny najdete v tématu [zabezpečení řídicího kanálu](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) .

## <a name="sampling"></a>Vzorkování

Azure Functions povolí vzorkování ve výchozím nastavení v konfiguraci. Další informace najdete v tématu [Konfigurace vzorkování](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

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
