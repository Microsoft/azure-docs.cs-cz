---
title: Agregace událostí Azure Service Fabric s využitím EventFlow | Dokumentace Microsoftu
description: Další informace o agregaci a shromažďování událostí pomocí využitím EventFlow pro monitorování a diagnostiku clustery Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 829d1ffd1ef75d18f0d87a127c43666703e8a756
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497362"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Agregace událostí a kolekce pomocí využitím EventFlow

[Microsoft diagnostiky s využitím EventFlow](https://github.com/Azure/diagnostics-eventflow) můžete směrování událostí z uzlu do jednoho nebo více cílů monitorování. Protože je zahrnutý jako balíček NuGet v projektu služby, využitím EventFlow kódu a konfigurace cestují ve službě, odstranění problém s konfigurací podle uzlu již bylo zmíněno dříve o Azure Diagnostics. Využitím EventFlow běží v rámci procesu služby a připojuje přímo k nakonfigurované výstupy. Z důvodu přímé připojení využitím EventFlow funguje pro Azure, kontejnerů a v místním nasazení služeb. Buďte opatrní při spuštění využitím EventFlow v s vysokou hustotou scénáře, jako například v kontejneru, protože každý kanál využitím EventFlow vytvoří externí připojení. Takže pokud hostujete několik procesů, získáte několik odchozích připojení! To není tolik zájmem zajistit u aplikací Service Fabric, protože všechny repliky `ServiceType` spustit ve stejném procesu, a to omezuje počet odchozích připojení. Využitím EventFlow také nabízí filtrování událostí tak, aby se odesílají pouze události, které odpovídají zadaného filtru.

## <a name="set-up-eventflow"></a>Nastavit využitím EventFlow

Využitím EventFlow binární soubory jsou k dispozici jako sada balíčků NuGet. Využitím EventFlow přidat do projektu služby Service Fabric, klikněte pravým tlačítkem na projekt v Průzkumníku řešení a zvolte možnost "Spravovat NuGet packages." Přepněte na kartu "Vyhledat" a vyhledejte "`Diagnostics.EventFlow`":

![Balíčky NuGet využitím EventFlow v uživatelské rozhraní Správce balíčků NuGet aplikace Visual Studio](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Zobrazí se seznam různých balíčků zobrazí, s popiskem "Vstupů" a "Výstupy". Využitím EventFlow podporuje různé jiné protokolování poskytovatelů a analyzátory. Služby hostování využitím EventFlow by měla obsahovat odpovídající balíčky v závislosti na zdroji a cíli pro protokoly aplikací. Kromě základní ServiceFabric balíček budete potřebovat alespoň jeden vstup a výstup nakonfigurované. Můžete například přidat následující balíčky k odesílání událostí EventSource do Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` například zachytávat data ze třídy EventSource služby a ze standardní EventSources *služby ServiceFabric* a *objektů actor Microsoft ServiceFabric*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (budeme odesílání protokolů do prostředku služby Azure Application Insights)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(umožňuje inicializaci kanálu využitím EventFlow z konfigurace služby Service Fabric a ohlásí případné potíže s odesílání diagnostických dat jako stavových sestav Service Fabric)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` balíček vyžaduje službu projekt cílit na .NET Framework 4.6 nebo novější. Nezapomeňte že nastavit odpovídající cílové rozhraní ve vlastnostech projektu před instalací tohoto balíčku.

Po instalaci všech balíčků, dalším krokem je ke konfiguraci a povolení využitím EventFlow ve službě.

## <a name="configure-and-enable-log-collection"></a>Nakonfigurovat a Povolit shromažďování protokolů
Odpovědná za zasílání protokolů využitím EventFlow kanál je vytvořený z specifikace uložené v konfiguračním souboru. `Microsoft.Diagnostics.EventFlow.ServiceFabric` Balíček nainstaluje výchozí konfigurační soubor využitím EventFlow pod `PackageRoot\Config` složku řešení s názvem `eventFlowConfig.json`. Tento konfigurační soubor musí být upravena k zachytávání dat ze služby výchozí `EventSource` třídy a všechny ostatní vstupy chcete konfigurace a odesílání dat na příslušné místo.

>[!NOTE]
>Pokud váš soubor projektu má formát Visual Studio 2017 `eventFlowConfig.json` soubor nebude přidán automaticky. Chcete-li vyřešit tím vytvoří soubor v `Config` složky a nastavte akci sestavení na `Copy if newer`. 

Tady je ukázka *eventFlowConfig.json* podle výše uvedených balíčků NuGet:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

Hodnota vlastnosti Name je název služby ServiceEventSource `EventSourceAttribute` použít pro třídu ServiceEventSource. Vše je uveden v `ServiceEventSource.cs` soubor, který je součástí kódu služby. Například následující fragment kódu je název ServiceEventSource *společnost. Application1 Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Všimněte si, že `eventFlowConfig.json` je soubor součástí balíčku pro konfiguraci služby. Změny tohoto souboru mohou být součástí upgrady full - nebo pouze pro konfiguraci služby, v souladu s kontroly stavu upgradu Service Fabric a automatického vrácení zpět, pokud se upgrade nezdaří. Další informace najdete v tématu [upgrade aplikace Service Fabric](service-fabric-application-upgrade.md).

*Filtry* oddíl konfigurace umožňuje dále přizpůsobit informace, na který budete absolvovat využitím EventFlow kanálu do výstupů, umožňuje vyřadit nebo obsahovat určité informace, nebo změnit strukturu data události. Další informace o filtrování najdete v tématu [využitím EventFlow filtry](https://github.com/Azure/diagnostics-eventflow#filters).

Posledním krokem je vytvoření instance využitím EventFlow kanálu v kódu při spuštění vaší služby umístěné v `Program.cs` souboru:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

Název předán jako parametr `CreatePipeline` metodu `ServiceFabricDiagnosticsPipelineFactory` je název *stavu entity* představující kanálu využitím EventFlow protokolu kolekce. Tento název se používá, pokud nalezne využitím EventFlow a chyby a ohlásí ji prostřednictvím subsystému stavu Service Fabric.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Použijte parametry aplikace a nastavení Service Fabric v eventFlowConfig

Využitím EventFlow podporuje, pomocí nastavení Service Fabric a parametry aplikace a zadejte nastavení s využitím EventFlow. Můžete odkazovat na parametry nastavení Service Fabric s využitím následující speciální syntaxi pro hodnoty:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` je název konfiguračního oddílu Service Fabric a `<setting-name>` je zadání hodnoty, který se použije ke konfiguraci nastavení využitím EventFlow nastavení konfigurace. Přečíst další informace o tom, jak to provést, přejděte na [podporu pro parametry aplikace a nastavení Service Fabric](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Ověření

Spusťte svoji službu, a podívejte se ladění okna výstup v sadě Visual Studio. Poté, co je služba spuštěna, začnou zobrazovat příslušné údaje důkaz, že vaše služba odesílá záznamy do výstupu, který jste nakonfigurovali. Přejděte k vaší platformě analýzu a vizualizaci událostí a potvrďte, že jste začali zobrazit protokoly nahoru (může trvat několik minut).

## <a name="next-steps"></a>Další postup

* [Události analýzy a vizualizace pomocí Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Události analýzy a vizualizace pomocí Log Analytics](service-fabric-diagnostics-event-analysis-oms.md)
* [Dokumentace ke službě využitím EventFlow](https://github.com/Azure/diagnostics-eventflow)
