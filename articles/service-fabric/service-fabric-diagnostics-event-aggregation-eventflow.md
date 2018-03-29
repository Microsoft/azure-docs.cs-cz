---
title: Azure Service Fabric událostí agregace s EventFlow | Microsoft Docs
description: Další informace o agregaci a shromažďování událostí pomocí EventFlow pro monitorování a Diagnostika Azure Service Fabric clusterů.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 37cae133dda2f274a83db1df37e798c5da58c539
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Seskupení událostí a kolekce pomocí EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) může směrovat události z uzlu na jeden nebo více monitorování místa určení. Vzhledem k tomu, že je zahrnut jako balíčku NuGet ve vašem projektu service, se službou, odstraňuje problém konfigurace podle uzlu, již bylo zmíněno dříve o Azure Diagnostics přenosu EventFlow kódu a konfigurace. EventFlow běží v rámci procesu vaší služby a připojuje přímo k nakonfigurované výstupy. Z důvodu přímé připojení EventFlow funguje pro Azure, kontejneru a nasazení služby v místě. Dávejte pozor, pokud spustíte EventFlow v podobě výkonných scénáře, jako třeba v kontejneru, protože každý kanál EventFlow vytvoří externí připojení. Takže pokud hostujete několik procesy, můžete načítat několik odchozí připojení! Tato akce není tolik důležité aplikace Service Fabric, protože všechny repliky `ServiceType` spustit ve stejném procesu, a to omezuje počet odchozí připojení. EventFlow také nabízí filtrování událostí tak, aby se odesílají jenom události, které odpovídají zadané filtru.

## <a name="set-up-eventflow"></a>Nastavit EventFlow

Binární soubory EventFlow jsou k dispozici jako sada balíčků NuGet. Pokud chcete přidat do projektu služby Service Fabric EventFlow, klikněte pravým tlačítkem na projekt v Průzkumníku řešení a zvolte "Balíčky NuGet spravovat". Přejděte na kartu "Vyhledat" a vyhledejte "`Diagnostics.EventFlow`":

![Balíčky EventFlow NuGet v uživatelské rozhraní Správce balíčků Visual Studio NuGet](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Zobrazí se seznam různých balíčků objeví, s popiskem "Vstup" a "Výstupy". EventFlow podporuje různé zprostředkovatelé různých protokolování a analyzátory. Služby hostování EventFlow by měla obsahovat příslušné balíčky v závislosti na zdroj a cíl pro protokoly aplikací. Kromě základní ServiceFabric balíčku musíte také aspoň jeden vstup a výstup nakonfigurovaný. Můžete například přidat následující balíčky určené k odesílání událostí EventSource Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` k zaznamenání dat od služby EventSource – třída a od standardní EventSources například *služby společnosti Microsoft ServiceFabric* a *Microsoft-ServiceFabric-aktéři*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (přidáme poslat protokoly prostředek Azure Application Insights)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(umožňuje inicializaci kanálu EventFlow z konfigurace služby Service Fabric a ohlásí případné potíže s odesílání diagnostických dat jako sestav stavu Service Fabric)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` balíček vyžaduje projekt služby, který má cílové rozhraní .NET Framework 4.6 nebo novější. Ujistěte se, že nastavíte příslušné cílové rozhraní v okně Vlastnosti projektu před instalací tohoto balíčku.

Po instalaci všech balíčků, dalším krokem je ke konfiguraci a povolení EventFlow ve službě.

## <a name="configure-and-enable-log-collection"></a>Konfigurovat a povolit protokol kolekce
Odpovědná za zasílání protokolů EventFlow kanálu je vytvořený z specifikaci uložené v konfiguračním souboru. `Microsoft.Diagnostics.EventFlow.ServiceFabric` Balíček nainstaluje výchozí konfigurační soubor EventFlow pod `PackageRoot\Config` složku řešení s názvem `eventFlowConfig.json`. Tento konfigurační soubor je potřeba upravit tak, aby zaznamenání dat z výchozí služba `EventSource` třídy a všechny ostatní vstupy chcete konfigurovat a odesílat data na příslušné místo.

>[!NOTE]
>Pokud váš projekt soubor má formát 2017 Visual Studio `eventFlowConfig.json` souboru nepřidají automaticky. Opravit to vytvoření souboru v `Config` složky a nastavit akce sestavení na `Copy if newer`. 

Zde je ukázka *eventFlowConfig.json* založené na balíčky NuGet uvedených výše:
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

Název služby ServiceEventSource je hodnota vlastnosti název `EventSourceAttribute` třídy ServiceEventSource použít. Všechny se vyskytuje v `ServiceEventSource.cs` souboru, který je součástí kódu služby. Například v následující fragment kódu je název ServiceEventSource *Moje_firma. Application1 Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Všimněte si, že `eventFlowConfig.json` soubor je součástí balíčku služby konfigurace. Změny tohoto souboru můžou být součástí úplné nebo konfigurace jen upgrady služby, podstoupí kontroly upgradu stavu Service Fabric a automatického vrácení zpět, dojde-li k selhání při upgradu. Další informace najdete v tématu [upgradu aplikace Service Fabric](service-fabric-application-upgrade.md).

*Filtry* část konfigurace můžete dále přizpůsobit informace, které se bude předat kanálem EventFlow výstupy umožňuje vyřadit nebo obsahovat určité informace, nebo změnit strukturu data události. Další informace o filtrování najdete v tématu [EventFlow filtry](https://github.com/Azure/diagnostics-eventflow#filters).

Posledním krokem je vytvoření instance EventFlow kanálu v kódu spuštění vaší služby, umístěný v `Program.cs` souboru:

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

Název předány jako parametr `CreatePipeline` metodu `ServiceFabricDiagnosticsPipelineFactory` je název *stavu entity* představující kanálu EventFlow protokolu kolekce. Tento název se používá, pokud EventFlow zaznamená a chybu a sestavy prostřednictvím subsystém stavu Service Fabric.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Použít nastavení Service Fabric a parametry aplikace v eventFlowConfig

EventFlow podporuje konfiguraci nastavení EventFlow pomocí Service Fabric nastavení a aplikace parametry. Můžete se podívat do Service Fabric nastavení parametrů pomocí této syntaxe speciální hodnoty:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` představuje název konfiguračního oddílu Service Fabric a `<setting-name>` je zajištění hodnotu, která se použije ke konfiguraci nastavení EventFlow nastavení konfigurace. Číst informace o tom, jak to provést, přejděte na [podporu pro Service Fabric nastavení a aplikace parametry](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Ověření

Spuštění služby a sledovat ladění výstup – okno v sadě Visual Studio. Po spuštění služby, měli byste začít zobrazuje důkaz, že vaše služba odesílá záznamy do výstupu, který jste nakonfigurovali. Přejděte k platformě analýzy a vizualizace událostí a ověřte, že protokoly spustili zobrazíte nahoru (může trvat několik minut).

## <a name="next-steps"></a>Další postup

* [Analýza události a vizualizace s Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analýza události a vizualizace s OMS](service-fabric-diagnostics-event-analysis-oms.md)
* [EventFlow dokumentace](https://github.com/Azure/diagnostics-eventflow)
