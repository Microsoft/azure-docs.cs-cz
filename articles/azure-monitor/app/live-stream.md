---
title: Diagnostika pomocí Live Metrics Stream – Azure Application Insights
description: Monitorujte svou webovou aplikaci v reálném čase s vlastními metrikami a Diagnostikujte problémy s živým informačním kanálem o selháních, trasováních a událostech.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: c50f7e57c520321d752d6d46e36c45c615b5e785
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803574"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: monitorování & diagnostiky s 1 sekundou latencí

Monitorujte svou živou webovou aplikaci v produkčním prostředí pomocí Live Metrics Stream (označuje se také jako QuickPulse) z [Application Insights](./app-insights-overview.md). Vyberte a filtrujte metriky a čítače výkonu pro sledování v reálném čase bez nutnosti zásahu do služby. Zkontrolujte trasování zásobníku z ukázkových neúspěšných žádostí a výjimek. Společně s [ladicím programem](./snapshot-debugger.md) [profileru](./profiler.md) a snímků Live Metrics Stream poskytuje výkonný a nenáročný diagnostický nástroj pro váš živý Web.

Pomocí Live Metrics Stream můžete:

* Ověřte, že je oprava vydaná, a to sledováním výkonu a počtu selhání.
* Sledujte účinek zátěže testu a Diagnostikujte problémy živě.
* Výběrem a filtrováním metrik, které chcete sledovat, se zaměřte na konkrétní testovací relace nebo vyfiltrujte známé problémy.
* Získejte trasování výjimek, když k nim dojde.
* Experimentujte s filtry, abyste našli nejrelevantnější klíčové ukazatele výkonu.
* Monitorujte libovolný čítač výkonu systému Windows v reálném čase.
* Snadno Identifikujte Server, u kterého dochází k problémům, a vyfiltrujte veškerý klíčový ukazatel výkonu a živý kanál jenom na tento server.

![Karta živá metriky](./media/live-stream/live-metric.png)

Pro aplikace ASP.NET, ASP.NET Core, Azure Functions, Java a Node.js se aktuálně podporují živé metriky.

## <a name="get-started"></a>Začínáme

1. Pokud chcete povolit živé metriky, postupujte podle pokynů pro konkrétní jazyk.
   * [ASP.NET](./asp-net.md) – aktivní metrika je ve výchozím nastavení povolená.
   * [ASP.NET Core](./asp-net-core.md)– aktivní metrika je ve výchozím nastavení povolená.
   * [.NET/.NET Core Console/pracovní proces](./worker-service.md)– živé metriky jsou ve výchozím nastavení povolené.
   * [Aplikace .NET – povolit používání kódu](#enable-livemetrics-using-code-for-any-net-application)
    * Ve výchozím nastavení jsou metriky [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) aktivní.
   * [Node.js](./nodejs.md#live-metrics)

2. V [Azure Portal](https://portal.azure.com)otevřete prostředek Application Insights pro vaši aplikaci a pak otevřete Live Stream.

3. [Zabezpečte řídicí kanál,](#secure-the-control-channel) Pokud ve svých filtrech používáte citlivá data, jako jsou názvy zákazníků.

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>Povolit LiveMetrics pomocí kódu pro libovolnou aplikaci .NET

I když je ve výchozím nastavení povolená služba LiveMetrics při připojování pomocí doporučených pokynů pro aplikace .NET, ukazuje následující postup ruční nastavení živých metrik.

1. Nainstalujte balíček NuGet [Microsoft. ApplicationInsights. PerfCounterCollector.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)
2. Následující vzorový kód aplikace konzoly ukazuje nastavení živých metrik.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

I když je výše uvedená ukázka pro konzolovou aplikaci, je možné použít stejný kód v aplikacích .NET. Pokud jsou povolené jiné TelemetryModules, které automaticky shromažďují telemetrii, je důležité zajistit, aby se stejná konfigurace použitá pro inicializaci těchto modulů používala i pro aktivní modul metrik.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Jak se Live Metrics Stream liší od Průzkumník metrik a analýzy?

| |Live Stream | Průzkumník metrik a analýzy |
|---|---|---|
|**Latence**|Data zobrazená během jedné sekundy|Agregované v průběhu minut|
|**Bez uchování**|Data se v grafu přetrvají a pak se zahodí.|[Data zachovaná po 90 dnech](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**Na vyžádání**|Data se streamují jenom v případě, že je otevřené podokno aktivní metriky. |Data se odesílají pokaždé, když je SDK nainstalovaná a povolená.|
|Zadejte možnost pro **bezplatnou** SKU.|Za Live Stream data se neúčtují žádné poplatky.|V souladu s [cenami](./pricing.md)
|**Vzorkování**|Přenáší se všechny vybrané metriky a čítače. Navzorkují se chyby a trasování zásobníku. |Události se dají [vzorkovat](./api-filtering-sampling.md) .|
|**Řídicí kanál**|Řídicí signály filtru se odesílají do sady SDK. Doporučujeme tento kanál zabezpečit.|Komunikace je jedním ze způsobů, jak na portál|

## <a name="select-and-filter-your-metrics"></a>Výběr a filtrování metriky

(K dispozici v ASP.NET, ASP.NET Core a Azure Functions (v2).)

Vlastní klíčový ukazatel výkonu můžete monitorovat tak, že použijete libovolné filtry na jakékoli Application Insights telemetrie z portálu. Klikněte na ovládací prvek filtru, který se zobrazí při přesunutí ukazatele myši na některý z grafů. Následující graf znázorňuje vlastní klíčový ukazatel výkonu počtu požadavků s filtry na atributy adresa URL a doba trvání. Ověřte filtry pomocí části Stream Preview, která zobrazuje živý informační kanál telemetrie, který odpovídá zadaným kritériím v libovolném časovém okamžiku.

![Filtr míry požadavků](./media/live-stream/filter-request.png)

Můžete monitorovat hodnotu odlišnou od počtu. Možnosti závisí na typu datového proudu, což může být jakákoli Application Insights telemetrie: požadavky, závislosti, výjimky, trasování, události nebo metriky. Může to být vlastní [měření](./api-custom-events-metrics.md#properties):

![Tvůrce dotazů podle míry požadavků s vlastní metrikou](./media/live-stream/query-builder-request.png)

Kromě Application Insights telemetrie můžete také monitorovat libovolný čítač výkonu systému Windows, a to tak, že ho vyberete z možností datového proudu a zadáte název čítače výkonu.

Živé metriky jsou agregované ve dvou bodech: místně na každém serveru a pak napříč všemi servery. Můžete změnit výchozí nastavení buď výběrem dalších možností v příslušných rozevíracích seznamech.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Ukázková telemetrie: vlastní živé diagnostické události
Ve výchozím nastavení zobrazuje živý kanál událostí ukázky neúspěšných žádostí a volání závislostí, výjimky, události a trasování. Kliknutím na ikonu filtru můžete zobrazit použitá kritéria v jakémkoli časovém okamžiku.

![Tlačítko Filtr](./media/live-stream/filter.png)

Stejně jako u metrik můžete zadat jakákoli libovolná kritéria pro Application Insights typy telemetrie. V tomto příkladu vybíráme konkrétní selhání požadavků a události.

![Tvůrce dotazů](./media/live-stream/query-builder.png)

> [!NOTE]
> V současné době pro kritéria založená na zprávách výjimky použijte zprávu o vnější výjimce. V předchozím příkladu pro odfiltrování neškodné výjimky se zprávou vnitřní výjimky (následuje oddělovač "<--") "klient odpojen". Použijte kritéria při čtení obsahu žádosti o zprávu Chyba.

Kliknutím zobrazíte podrobnosti položky v živém kanálu. Informační kanál můžete pozastavit buď kliknutím na tlačítko **pozastavit** nebo pouhým posouváním, nebo kliknutím na položku. Živý kanál bude pokračovat, až se posunete zpět na začátek, nebo kliknutím na čítač shromážděných položek v době, kdy byl pozastaven.

![Snímek obrazovky znázorňující ukázkové okno telemetrie s vybranou výjimkou a podrobnosti o výjimce, které se zobrazí v dolní části okna.](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Filtrovat podle instance serveru

Pokud chcete monitorovat určitou instanci role serveru, můžete filtrovat podle serveru. Chcete-li filtrovat, vyberte název serveru v části *servery*.

![Ukázka neaktivních selhání](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Zabezpečení řídicího kanálu

> [!NOTE]
> V současné době můžete nastavit jenom ověřený kanál pomocí monitorování založeného na kódu a nemůžete ověřovat servery pomocí připojení bez kódu.

Vlastní kritéria filtrů, která zadáte v portálu Live Metrics, se odešlou zpět do komponenty živé metriky v sadě Application Insights SDK. Filtry mohou potenciálně obsahovat citlivé informace, jako jsou například KódZákazníka. Kanál můžete nastavit jako zabezpečený pomocí tajného klíče rozhraní API, a to i pomocí klíče instrumentace.

### <a name="create-an-api-key"></a>Vytvoření klíče rozhraní API

![Klíč rozhraní API > vytvořit klíč rozhraní API – ](./media/live-stream/api-key.png)
 ![ karta vytvořit klíč rozhraní API. Vyberte "ověřit kontrolní kanál SDK" a pak "vygenerovat klíč".](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>Přidat klíč rozhraní API do konfigurace

### <a name="aspnet"></a>ASP.NET

Do souboru applicationinsights.config přidejte AuthenticationApiKey do QuickPulseTelemetryModule:

```XML
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

Pro [ASP.NET Core](./asp-net-core.md) aplikace postupujte podle následujících pokynů.

Upravte `ConfigureServices` soubor Startup.cs následujícím způsobem:

Přidejte následující obor názvů.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Pak upravte `ConfigureServices` metodu následujícím způsobem.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

Další informace o konfiguraci aplikací ASP.NET Core najdete v našem návodu ke [konfiguraci modulů telemetrie v ASP.NET Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules).

### <a name="workerservice"></a>WorkerService

V případě aplikací [WorkerService](./worker-service.md) postupujte podle následujících pokynů.

Přidejte následující obor názvů.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Dále přidejte následující řádek před voláním `services.AddApplicationInsightsTelemetryWorkerService` .

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Další informace o konfiguraci aplikací WorkerService najdete v našem návodu ke [konfiguraci modulů telemetrie v WorkerServices](./worker-service.md#configuring-or-removing-default-telemetrymodules).

### <a name="azure-function-apps"></a>Aplikace Azure Functions

V případě aplikací Azure Function App (v2) lze zabezpečení kanálu pomocí klíče rozhraní API provést pomocí proměnné prostředí.

Vytvořte klíč rozhraní API z prostředku Application Insights a v **nastavení > konfiguraci** pro Function App. Vyberte **Nastavení nové aplikace** a zadejte název `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` a hodnotu, která odpovídá vašemu klíči rozhraní API.

Pokud ale znáte všechny připojené servery a důvěřujete jim, můžete si vyzkoušet vlastní filtry bez ověřeného kanálu. Tato možnost je k dispozici po dobu šesti měsíců. Toto přepsání se vyžaduje po každé nové relaci, nebo když se nový server dostane do online režimu.

![Možnosti ověřování živých metrik](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Důrazně doporučujeme, abyste nastavili ověřený kanál před zadáním potenciálně citlivých informací, jako je například KódZákazníka v kritériích filtru.
>

## <a name="supported-features-table"></a>Tabulka podporovaných funkcí

| Jazyk                         | Základní metriky       | Metriky výkonu | Vlastní filtrování    | Ukázková telemetrie    | PROCESOR rozdělený podle procesu |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET Framework                   | Podporováno (V 2.7.2 +) | Podporováno (V 2.7.2 +) | Podporováno (V 2.7.2 +) | Podporováno (V 2.7.2 +) | Podporováno (V 2.7.2 +)  |
| .NET Core (Target =. NET Framework)| Podporováno (V 2.4.1 +) | Podporováno (V 2.4.1 +) | Podporováno (V 2.4.1 +) | Podporováno (V 2.4.1 +) | Podporováno (V 2.4.1 +)  |
| .NET Core (Target =. NET Core)     | Podporováno (V 2.4.1 +) | Podporuje se*          | Podporováno (V 2.4.1 +) | Podporováno (V 2.4.1 +) | **Nepodporováno**    |
| Azure Functions v2               | Podporováno           | Podporováno           | Podporováno           | Podporováno           | **Nepodporováno**    |
| Java                             | Podporováno (V 2.0.0 +) | Podporováno (V 2.0.0 +) | **Nepodporováno**   | **Nepodporováno**   | **Nepodporováno**    |
| Node.js                          | Podporováno (V 1.3.0 +) | Podporováno (V 1.3.0 +) | **Nepodporováno**   | Podporováno (V 1.3.0 +) | **Nepodporováno**    |

Základní metriky zahrnují četnost požadavků, závislostí a výjimek. Metriky výkonu (čítače výkonu) obsahují paměť a procesor. Ukázková telemetrie zobrazuje datový proud s podrobnými informacemi o neúspěšných požadavcích a závislostech, výjimkách, událostech a trasováních.

 \* Podpora PerfCounters se mírně liší napříč verzemi .NET Core, které necílí na .NET Framework:

- Metriky PerfCounters se podporují při použití v Azure App Service pro Windows. (AspNetCore SDK verze 2.4.1 nebo vyšší)
- PerfCounters se podporují, když je aplikace spuštěná na LIBOVOLNÝch počítačích s Windows (virtuální počítač nebo cloudová služba nebo na Prem atd.). (AspNetCore SDK verze 2.7.1 nebo vyšší), ale pro aplikace cílené na .NET Core 2,0 nebo vyšší.
- PerfCounters se podporují, pokud je aplikace spuštěná kdekoli (Linux, Windows, App Service pro Linux, kontejnery atd.) v nejnovějších verzích (tj. AspNetCore SDK verze 2.8.0 nebo vyšší), ale jenom pro aplikace cílené na .NET Core 2,0 nebo vyšší.

## <a name="troubleshooting"></a>Řešení potíží

Live Metrics Stream používá jiné IP adresy než jiné telemetrie Application Insights. Ujistěte se, že jsou [tyto IP adresy](./ip-addresses.md) v bráně firewall otevřené. Také ověřte, zda jsou v bráně firewall serverů otevřeny [Odchozí porty pro Live Metrics Stream](./ip-addresses.md#outgoing-ports) .

## <a name="next-steps"></a>Další kroky

* [Monitorování využití pomocí Application Insights](./usage-overview.md)
* [Pomocí diagnostického vyhledávání](./diagnostic-search.md)
* [Profiler](./profiler.md)
* [Snapshot Debugger](./snapshot-debugger.md)
