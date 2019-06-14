---
title: Funkce podporované služby Azure Application Insights – služba Azure Functions | Dokumentace Microsoftu
description: Funkce podporované Application Insights pro službu Azure Functions
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 0199d8f0c4a76a10fffcab7cf2819643d0ac2d68
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075345"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Funkce podporované Application Insights pro službu Azure Functions

Azure Functions nabízí [vestavěná integrace](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) pomocí Application Insights, která je dostupná přes rozhraní objektu ILogger. Níže je seznam aktuálně podporovaných funkcí. Přečtěte si průvodce Azure Functions pro [Začínáme](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Podporované funkce

| Azure Functions                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Automatické shromažďování**        |                 |                   |               
| &bull; Požadavky                     | Ano             | Ano               | 
| &bull; Výjimky                   | Ano             | Ano               | 
| &bull; Čítače výkonu         | Ano             | Ano               |
| &bull; Závislosti                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Ano               | 
| | | | 
| **Podporované funkce**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Ano             | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash; Zabezpečený kanál ovládacího prvku|                 | Ano               | 
| &bull; Vzorkování                     | Ano             | Ano               | 
| &bull; Prezenční signály                   |                 | Ano               | 
| | | | 
| **Korelace**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Ano               | 
| &bull; EventHub                       |                   | Ano               | 
| | | | 
| **Konfigurovatelné**                      |                   |                   |           
| &bull;Plně konfigurovatelné.<br/>Zobrazit [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) pokyny.<br/>Zobrazit [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) pro všechny možnosti.               |                   | Ano                   | 


## <a name="performance-counters"></a>Čítače výkonu

Automatické shromažďování čítačů výkonu fungují jenom počítače s Windows.


## <a name="live-metrics--secure-control-channel"></a>Živé metriky a zabezpečený kanál ovládacího prvku

Vlastní kritéria filtry, které jste zadali odesílají zpět do komponenty Live Metrics v Application Insights SDK. Filtry můžou potenciálně obsahovat citlivé informace, jako je například customerIDs. Kanál můžete zabezpečit pomocí tajného klíče rozhraní API. Zobrazit [zabezpečený kanál ovládací prvek](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) pokyny.

## <a name="sampling"></a>Vzorkování

Služba Azure Functions umožňuje v jejich konfigurace ve výchozím nastavení vzorkování. Další informace najdete v tématu [konfigurace vzorkování](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Pokud váš projekt je závislá na sadu SDK Application Insights provedete ruční telemetrických dat, sledování, může docházet neobvyklé chování, pokud vaše konfigurace vzorkování se liší od konfigurace vzorkování funkce. 

Doporučujeme používat stejnou konfiguraci jako funkce. S **funkce v2**, dostanete stejnou konfiguraci pomocí vkládání závislostí v váš konstruktor:

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
