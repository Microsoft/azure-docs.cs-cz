---
title: Azure Service Fabric agregace událostí s EventFlow
description: Další informace o agregaci a shromažďování událostí pomocí EventFlow pro monitorování a diagnostiku clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde24657cc8ed78b91e72df16d51df4077a6e030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463091"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Agregace a kolekce událostí pomocí EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) může směrovat události z uzlu do jednoho nebo více cílů monitorování. Vzhledem k tomu, že je součástí balíčku NuGet v projektu služby, EventFlow kód a konfigurace cestování se službou, odstranění problému konfigurace na uzel je uvedeno dříve o Azure Diagnostics. EventFlow běží v rámci procesu vaší služby a připojuje se přímo k nakonfigurovaným výstupům. Z důvodu přímého připojení EventFlow funguje pro nasazení Azure, kontejnerů a místních služeb. Buďte opatrní, pokud spustíte EventFlow ve scénářích s vysokou hustotou, například v kontejneru, protože každý kanál EventFlow vytvoří externí připojení. Pokud tedy hostujete několik procesů, získáte několik odchozích připojení! To není tolik obavy pro aplikace Service Fabric, protože `ServiceType` všechny repliky spustit ve stejném procesu, a to omezuje počet odchozích připojení. EventFlow také nabízí filtrování událostí, takže jsou odesílány pouze události, které odpovídají zadanému filtru.

## <a name="set-up-eventflow"></a>Nastavit EventFlow

Binární soubory EventFlow jsou k dispozici jako sada balíčků NuGet. Chcete-li přidat EventFlow do projektu služby Fabric služby, klikněte pravým tlačítkem myši na projekt v Průzkumníku řešení a zvolte "Spravovat balíčky NuGet." Přepněte na kartu "Procházet"`Diagnostics.EventFlow`a vyhledejte " ":

![Balíčky EventFlow NuGet v uzly správce balíčků Sady Visual Studio NuGet](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Zobrazí se seznam různých balíčků označených "Vstupy" a "Výstupy". EventFlow podporuje různé různé poskytovatele protokolování a analyzátory. Služba hostující EventFlow by měla obsahovat příslušné balíčky v závislosti na zdroji a cíli protokolů aplikací. Kromě základního balíčku ServiceFabric potřebujete také alespoň jeden vstupní a výstupní nakonfigurovaný. Můžete například přidat následující balíčky pro odesílání událostí EventSource do Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource`pro sběr dat z třídy EventSource služby a ze standardních zdrojů událostí, jako jsou *microsoft-ServiceFabric-Services* a *Microsoft-ServiceFabric-Actors*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`(budeme odesílat protokoly do prostředku Azure Application Insights)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(umožňuje inicializaci kanálu EventFlow z konfigurace služby Service Fabric a hlásí všechny problémy s odesíláním diagnostických dat jako sestavy stavu Service Fabric)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource`balíček vyžaduje, aby projekt služby cílil na rozhraní .NET Framework 4.6 nebo novější. Ujistěte se, že jste před instalací tohoto balíčku nastavili příslušnou cílovou architekturu ve vlastnostech projektu.

Po instalaci všech balíčků je dalším krokem konfigurace a povolení EventFlow ve službě.

## <a name="configure-and-enable-log-collection"></a>Konfigurace a povolení kolekce protokolů
Kanál EventFlow zodpovědný za odesílání protokolů je vytvořen ze specifikace uložené v konfiguračním souboru. Balíček `Microsoft.Diagnostics.EventFlow.ServiceFabric` nainstaluje počáteční konfigurační soubor EventFlow do `PackageRoot\Config` složky řešení s názvem `eventFlowConfig.json`. Tento konfigurační soubor je třeba upravit `EventSource` tak, aby zachycovací data z výchozí třídy služby a všechny ostatní vstupy, které chcete konfigurovat, a odesílat data na příslušné místo.

>[!NOTE]
>Pokud má soubor projektu formát VisualStudio `eventFlowConfig.json` 2017, nebude automaticky přidán. Chcete-li tento problém `Config` vyřešit, vytvořte `Copy if newer`soubor ve složce a nastavte akci sestavení na . 

Zde je ukázková *událostFlowConfig.json* založená na výše uvedených balíčcích NuGet:
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

Název serviceEventSource je hodnota Name vlastnost `EventSourceAttribute` i ap pro Třídu ServiceEventSource. Vše je zadáno `ServiceEventSource.cs` v souboru, který je součástí kódu služby. Například v následujícím fragmentu kódu je název ServiceEventSource *MyCompany-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Všimněte `eventFlowConfig.json` si, že soubor je součástí balíčku konfigurace služby. Změny tohoto souboru mohou být zahrnuty do úplných nebo konfiguračních upgradů služby, s výhradou kontrolstavu upgradu service fabric a automatického vrácení zpět, pokud dojde k selhání upgradu. Další informace naleznete v tématu [Service Fabric upgrade aplikace](service-fabric-application-upgrade.md).

Sekce *filtry* v konfiguraci umožňuje dále přizpůsobit informace, které budou procházet kanálem EventFlow, k výstupům, což vám umožní vynechat nebo zahrnout určité informace nebo změnit strukturu dat události. Další informace o filtrování naleznete v [tématu EventFlow filters](https://github.com/Azure/diagnostics-eventflow#filters).

Posledním krokem je vytvoření instance kanálu EventFlow v kódu spuštění vaší `Program.cs` služby, který se nachází v souboru:

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

Název předaný jako `CreatePipeline` parametr metody `ServiceFabricDiagnosticsPipelineFactory` je název *entity stavu* představující kanál kolekce protokolu EventFlow. Tento název se používá, pokud EventFlow narazí a chyby a hlásí prostřednictvím subsystému stavu Service Fabric.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Použití nastavení service fabric a parametrů aplikace v eventFlowConfig

EventFlow podporuje použití nastavení Service Fabric a parametrů aplikace ke konfiguraci nastavení EventFlow. Můžete odkazovat na parametry nastavení Service Fabric pomocí této speciální syntaxe pro hodnoty:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>`je název části konfigurace Service Fabric `<setting-name>` a je nastavení konfigurace poskytující hodnotu, která bude použita ke konfiguraci nastavení EventFlow. Další informace o tom, jak to provést, naleznete v části [Podpora nastavení service fabric a parametrů aplikace](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Ověření

Spusťte službu a sledujte okno výstupu ladění v sadě Visual Studio. Po spuštění služby byste měli začít vidět důkazy, že vaše služba odesílá záznamy na výstup, který jste nakonfigurovali. Přejděte na platformu pro analýzu událostí a vizualizaci a potvrďte, že se začaly zobrazovat protokoly (může trvat několik minut).

## <a name="next-steps"></a>Další kroky

* [Analýza událostí a vizualizace s přehledy aplikací](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analýza událostí a vizualizace pomocí protokolů Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* [Dokumentace eventflow](https://github.com/Azure/diagnostics-eventflow)
