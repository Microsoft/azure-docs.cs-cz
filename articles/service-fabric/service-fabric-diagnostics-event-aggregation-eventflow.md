---
title: Agregace událostí v Azure Service Fabric s využitím eventflow
description: Naučte se agregovat a shromažďovat události pomocí využitím eventflow pro monitorování a diagnostiku clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: a80eea5a50aa7b1e441049eeb2cae381994cd3ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006332"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Agregace a shromažďování událostí pomocí využitím eventflow

[Microsoft Diagnostic využitím eventflow](https://github.com/Azure/diagnostics-eventflow) může směrovat události z uzlu do jednoho nebo více cílů monitorování. Protože je součástí projektu služby, jako balíček NuGet, využitím eventflow kód a konfigurace s touto službou, čímž se odstraní problém s konfigurací jednotlivých uzlů zmíněný Dříve o Azure Diagnostics. Využitím eventflow běží v rámci procesu služby a připojuje se přímo k nakonfigurovaným výstupům. Kvůli přímému připojení využitím eventflow funguje pro nasazení Azure, kontejneru a místních služeb. Buďte opatrní při spuštění využitím eventflow ve scénářích s vysokou hustotou, jako je například v kontejneru, protože každý kanál využitím eventflow vytváří externí připojení. Pokud tedy budete hostovat několik procesů, získáte několik odchozích připojení. Nejedná se o problém s Service Fabric aplikacemi, protože všechny repliky se `ServiceType` spouštějí ve stejném procesu a omezuje počet odchozích připojení. Využitím eventflow také nabízí filtrování událostí, aby se odesílaly jenom události, které odpovídají zadanému filtru.

## <a name="set-up-eventflow"></a>Nastavení využitím eventflow

Binární soubory využitím eventflow jsou k dispozici jako sada balíčků NuGet. Chcete-li přidat využitím eventflow do projektu služby Service Fabric, klikněte pravým tlačítkem myši na projekt v Průzkumník řešení a vyberte možnost spravovat balíčky NuGet. Přepněte na kartu Procházet a vyhledejte " `Diagnostics.EventFlow` ":

![Využitím eventflow balíčky NuGet v uživatelském rozhraní Správce balíčků NuGet sady Visual Studio](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Zobrazí se seznam různých balíčků, které jsou označené "vstupy" a "výstupy". Využitím eventflow podporuje různé zprostředkovatele protokolování a analyzátory. Služba hostující využitím eventflow by měla zahrnovat příslušné balíčky v závislosti na zdroji a cíli pro protokoly aplikací. Kromě základního balíčku ServiceFabric potřebujete také nakonfigurovat alespoň jeden vstup a výstup. Můžete například přidat následující balíčky pro odeslání událostí EventSource do Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource`zaznamenání dat ze třídy EventSource služby a ze standardních EventSources, jako je například *Microsoft-ServiceFabric-Services* a *Microsoft-ServiceFabric-Actors*
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (protokoly pošleme do prostředku Azure Application Insights)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(umožňuje inicializaci kanálu využitím eventflow z konfigurace služby Service Fabric a oznamuje případné problémy s odesíláním diagnostických dat jako Service Fabricch sestav stavu)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` balíček vyžaduje, aby projekt služby byl cílový .NET Framework 4,6 nebo novější. Před instalací tohoto balíčku se ujistěte, že jste nastavili příslušnou cílovou architekturu ve vlastnostech projektu.

Po instalaci všech balíčků je dalším krokem konfigurace a povolení využitím eventflow ve službě.

## <a name="configure-and-enable-log-collection"></a>Konfigurace a povolení shromažďování protokolů
Kanál využitím eventflow zodpovědný za odeslání protokolů se vytvoří ze specifikace uložené v konfiguračním souboru. `Microsoft.Diagnostics.EventFlow.ServiceFabric`Balíček nainstaluje spouštěcí konfigurační soubor využitím eventflow ve `PackageRoot\Config` složce řešení s názvem `eventFlowConfig.json` . Tento konfigurační soubor se musí upravit, aby zachytával data z výchozí `EventSource` třídy služby a všech dalších vstupů, které chcete nakonfigurovat, a odesílat data na příslušné místo.

>[!NOTE]
>Pokud soubor projektu má formát VisualStudio 2017, `eventFlowConfig.json` soubor se nepřidá automaticky. Chcete-li tento problém vyřešit, vytvořte soubor ve `Config` složce a nastavte akci sestavení na `Copy if newer` . 

Tady je ukázka *eventFlowConfig.jsna* základě balíčků NuGet uvedených výše:
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

Název ServiceEventSource služby je hodnota vlastnosti název `EventSourceAttribute` použité pro třídu ServiceEventSource. Je vše zadáno v `ServiceEventSource.cs` souboru, který je součástí kódu služby. Například v následujícím fragmentu kódu název ServiceEventSource je společnost *-application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Všimněte si, že `eventFlowConfig.json` soubor je součástí konfiguračního balíčku služby. Změny v tomto souboru můžete zahrnout do upgradu služby na základě úplného nebo pouze konfigurace, pokud dojde k selhání upgradu, Service Fabric upgradovat kontroly stavu a automatické vrácení zpět. Další informace naleznete v tématu [Service Fabric upgrade aplikace](service-fabric-application-upgrade.md).

Oddíl *filtry* v konfiguraci umožňuje dále přizpůsobit informace, které se procházejí kanálem využitím eventflow, do výstupů, což vám umožní vyřadit nebo zahrnout určité informace nebo změnit strukturu dat událostí. Další informace o filtrování najdete v tématu [využitím eventflow Filters](https://github.com/Azure/diagnostics-eventflow#filters).

Posledním krokem je vytvoření kanálu využitím eventflow ve spouštěcím kódu vaší služby, který je umístěný v `Program.cs` souboru:

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

Název předaný jako parametr `CreatePipeline` metody `ServiceFabricDiagnosticsPipelineFactory` je název *entity stavu* představující kanál shromažďování protokolů využitím eventflow. Tento název se používá v případě, že využitím eventflow dojde k chybě a ohlásí ho prostřednictvím subsystému Service Fabric Health.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Použití nastavení Service Fabric a parametrů aplikace v eventFlowConfig

Využitím eventflow podporuje konfiguraci nastavení využitím eventflow pomocí nastavení Service Fabric a parametrů aplikace. Můžete se podívat na parametry Service Fabric nastavení pomocí této speciální syntaxe pro hodnoty:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` je název konfiguračního oddílu Service Fabric a jedná se o `<setting-name>` nastavení konfigurace, které poskytuje hodnotu, která se použije ke konfiguraci nastavení využitím eventflow. Další informace o tom, jak to provést, najdete v článku [Podpora nastavení Service Fabric a parametrů aplikace](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Ověření

Spusťte službu a sledujte okno výstup ladění v aplikaci Visual Studio. Po spuštění služby byste měli začít zobrazovat legitimaci, že vaše služba odesílá záznamy do výstupu, který jste nakonfigurovali. Přejděte k vaší platformě pro analýzu událostí a vizualizace a potvrďte, že se protokoly začaly zobrazovat (může to trvat několik minut).

## <a name="next-steps"></a>Další kroky

* [Analýza a vizualizace událostí pomocí Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analýza a vizualizace událostí pomocí protokolů Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* [Dokumentace k využitím eventflow](https://github.com/Azure/diagnostics-eventflow)
