---
title: Monitorování využití a výkonu desktopových aplikací pro Windows
description: Analyzujte využití a výkon vaší desktopové aplikace Windows pomocí Application Insights.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 15331494b40021f10c162fba75abf6fe88d2d419
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100583314"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitorování využití a výkonu klasických desktopových aplikací pro Windows

Aplikace hostované místně, v Azure a jiných cloudech mohou využít všech výhod Application Insights. Jediným omezením je nutnost [povolení komunikace](./ip-addresses.md) se službou Application Insights. Pro monitorování aplikací pro Univerzální platformu Windows (UPW) doporučujeme používat sadu [Visual Studio App Center](../app/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Odeslání telemetrie do Application Insights z klasické aplikace pro Windows
1. Na webu [Azure Portal](https://portal.azure.com)[vytvořte prostředek Application Insights](./create-new-resource.md). 
2. Zkopírujte klíč instrumentace.
3. V sadě Visual Studio upravte balíčky NuGet projektu aplikace a přidejte Microsoft.ApplicationInsights.WindowsServer. (Nebo vyberte Microsoft. ApplicationInsights, pokud chcete jenom základní rozhraní API, a to bez standardních modulů kolekce telemetrie.)
4. Nastavte klíč instrumentace, buď ve vašem kódu:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*váš klíč*`";`
   
    nebo v souboru ApplicationInsights.config (pokud jste nainstalovali jeden ze standardních balíčků telemetrie):
   
    `<InstrumentationKey>`*váš klíč*`</InstrumentationKey>` 
   
    Pokud používáte soubor ApplicationInsights.config, ujistěte se, že jsou jeho vlastnosti v Průzkumníku řešení nastavené na: **Build Action = Content, Copy to Output Directory = Copy**.
5. [Použijte rozhraní API](./api-custom-events-metrics.md) k odesílání telemetrie.
6. Spusťte aplikaci a podívejte se na telemetrii v prostředku, který jste vytvořili v Azure Portal.

## <a name="example-code"></a><a name="telemetry"></a>Příklad kódu

```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnFormClosing(System.Windows.Forms.FormClosingEventArgs e)
        {
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnFormClosing(e);
        }
        
        ...
```

## <a name="override-storage-of-computer-name"></a>Přepsat úložiště názvu počítače

Ve výchozím nastavení bude tato sada SDK shromažďovat a ukládat název počítače, který generuje telemetrii.

Název počítače používá Application Insights [starší verze Enterprise (na jeden uzel) cenová úroveň](./pricing.md#legacy-enterprise-per-node-pricing-tier) pro interní účely fakturace. Ve výchozím nastavení, pokud použijete inicializátor telemetrie k přepsání `telemetry.Context.Cloud.RoleInstance` , bude odeslána samostatná vlastnost, `ai.internal.nodeName` která bude stále obsahovat hodnotu názvu počítače. Tato hodnota se nebude ukládat spolu s vaší Application Insights telemetrie, ale používá se interně při příjmu, aby se zajistila zpětná kompatibilita se starším modelem fakturace na základě uzlů.

Pokud se nacházíte na [cenové úrovni starší verze Enterprise (na uzel)](./pricing.md#legacy-enterprise-per-node-pricing-tier) a jednoduše potřebujete přepsat úložiště pro název počítače, použijte inicializátor telemetrie:

**Pište vlastní TelemetryInitializer, jak je uvedeno níže.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

Vytvořte instanci inicializátoru v `Program.cs` `Main()` metodě níže nastavením klíče instrumentace:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>Přepsat přenos názvu počítače

Pokud nejste na [cenové úrovni starší verze Enterprise (na uzel)](./pricing.md#legacy-enterprise-per-node-pricing-tier) a chcete zcela zabránit tomu, aby se všechny telemetrie obsahující název počítače odesílaly, musíte použít procesor telemetrie.

### <a name="telemetry-processor"></a>Procesor telemetrie

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

Vytvořte instanci procesoru telemetrie v `Program.cs` `Main()` metodě níže nastavením klíče instrumentace:

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> I když můžete technicky používat procesor telemetrie, jak je popsáno výše, i když pracujete na [cenové úrovni starší verze Enterprise (na uzel)](./pricing.md#legacy-enterprise-per-node-pricing-tier), výsledkem bude potenciální fakturace v důsledku nesprávného rozlišení uzlů pro ceny za uzel.

## <a name="next-steps"></a>Další kroky
* [Vytvoření řídicího panelu](./overview-dashboard.md)
* [Diagnostické hledání](./diagnostic-search.md)
* [Zkoumání metrik](../essentials/metrics-charts.md)
* [Psaní analytických dotazů](../logs/log-query-overview.md)

