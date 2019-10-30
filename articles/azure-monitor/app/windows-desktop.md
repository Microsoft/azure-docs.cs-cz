---
title: Monitorování využití a výkonu desktopových aplikací pro Windows
description: Analyzujte využití a výkon vaší desktopové aplikace Windows pomocí Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/29/2019
ms.openlocfilehash: a9dfc32a0f33db5639d5f74667a90a248dc358a1
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73052458"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitorování využití a výkonu klasických desktopových aplikací pro Windows

Aplikace hostované místně, v Azure a jiných cloudech mohou využít všech výhod Application Insights. Jediným omezením je nutnost [povolení komunikace](../../azure-monitor/app/ip-addresses.md) se službou Application Insights. Pro monitorování aplikací pro Univerzální platformu Windows (UPW) doporučujeme používat sadu [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Odeslání telemetrie do Application Insights z klasické aplikace pro Windows
1. Na webu [Azure Portal](https://portal.azure.com) [vytvořte prostředek Application Insights](../../azure-monitor/app/create-new-resource.md ). Jako typ aplikace vyberte aplikaci ASP.NET.
2. Zkopírujte klíč instrumentace. Klíč najdete v rozevírací nabídce Základy nového prostředku, který jste právě vytvořili. 
3. V sadě Visual Studio upravte balíčky NuGet projektu aplikace a přidejte Microsoft.ApplicationInsights.WindowsServer. (Nebo zvolte Microsoft.ApplicationInsights, pokud chcete prosté rozhraní API bez standardních modulů kolekce telemetrie.)
4. Nastavte klíč instrumentace, buď ve vašem kódu:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*váš klíč*`";`
   
    nebo v souboru ApplicationInsights.config (pokud jste nainstalovali jeden ze standardních balíčků telemetrie):
   
    `<InstrumentationKey>`*váš klíč*`</InstrumentationKey>` 
   
    Pokud používáte soubor ApplicationInsights.config, ujistěte se, že jsou jeho vlastnosti v Průzkumníku řešení nastavené na: **Build Action = Content, Copy to Output Directory = Copy**.
5. [Použijte rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md) k odesílání telemetrie.
6. Spusťte aplikaci a podívejte se na telemetrii v prostředku, který jste vytvořili v Azure Portal.

## <a name="telemetry"></a>Příklad kódu
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

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>Přepsat úložiště názvu počítače

Ve výchozím nastavení bude tato sada SDK shromažďovat a ukládat název počítače, který generuje telemetrii. Pro přepsání kolekce potřebujete použít inicializátor telemetrie:

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
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
Vytvořte instanci inicializátoru v metodě `Program.cs` `Main()` níže v části nastavení klíče instrumentace:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>Další kroky
* [Vytvoření řídicího panelu](../../azure-monitor/app/overview-dashboard.md)
* [Diagnostické vyhledávání](../../azure-monitor/app/diagnostic-search.md)
* [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md)
* [Psaní analytických dotazů](../../azure-monitor/app/analytics.md)

