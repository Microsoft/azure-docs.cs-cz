---
title: Diagnostika pomocí Live Metrics Stream – Azure Application Insights
description: Monitorujte svou webovou aplikaci v reálném čase s vlastními metrikami a Diagnostikujte problémy s živým informačním kanálem o selháních, trasováních a událostech.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: e554595a7a88e1455f7426636dc69db99a7d3e94
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86166480"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: monitorování & diagnostiky s 1 sekundou latencí

Monitorujte svou živou webovou aplikaci v produkčním prostředí pomocí Live Metrics Stream z [Application Insights](../../azure-monitor/app/app-insights-overview.md). Vyberte a filtrujte metriky a čítače výkonu pro sledování v reálném čase bez nutnosti zásahu do služby. Zkontrolujte trasování zásobníku z ukázkových neúspěšných žádostí a výjimek. Společně s [ladicím programem](../../azure-monitor/app/snapshot-debugger.md) [profileru](../../azure-monitor/app/profiler.md) a snímků Live Metrics Stream poskytuje výkonný a nenáročný diagnostický nástroj pro váš živý Web.

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

1. [Nainstalujte Application Insights](../../azure-monitor/azure-monitor-app-hub.yml) do své aplikace.
2. K povolení živého streamu metrik se vyžaduje kromě standardních Application Insights balíčků [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) .
3. **Aktualizujte na nejnovější verzi** balíčku Application Insights. V aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt a vyberte možnost **Spravovat balíčky NuGet**. Otevřete kartu **aktualizace** a vyberte všechny balíčky Microsoft. ApplicationInsights. *.

    Znovu nasaďte aplikaci.

3. V [Azure Portal](https://portal.azure.com)otevřete prostředek Application Insights pro vaši aplikaci a pak otevřete Live Stream.

4. [Zabezpečte řídicí kanál,](#secure-the-control-channel) Pokud ve svých filtrech používáte citlivá data, jako jsou názvy zákazníků.

### <a name="no-data-check-your-server-firewall"></a>Žádná data? Ověřit bránu firewall serveru

Ověřte, že [Odchozí porty pro Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) jsou otevřené v bráně firewall vašich serverů.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Jak se Live Metrics Stream liší od Průzkumník metrik a analýzy?

| |Live Stream | Průzkumník metrik a analýzy |
|---|---|---|
|**Latence**|Data zobrazená během jedné sekundy|Agregované v průběhu minut|
|**Bez uchování**|Data se v grafu přetrvají a pak se zahodí.|[Data zachovaná po 90 dnech](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|**Na vyžádání**|Data se streamují jenom v případě, že je otevřené podokno aktivní metriky. |Data se odesílají pokaždé, když je SDK nainstalovaná a povolená.|
|Zadejte možnost pro **bezplatnou** SKU.|Za Live Stream data se neúčtují žádné poplatky.|V souladu s [cenami](../../azure-monitor/app/pricing.md)
|**Vzorkování**|Přenáší se všechny vybrané metriky a čítače. Navzorkují se chyby a trasování zásobníku. TelemetryProcessors se neaplikují.|Události se dají [vzorkovat](../../azure-monitor/app/api-filtering-sampling.md) .|
|**Řídicí kanál**|Řídicí signály filtru se odesílají do sady SDK. Doporučujeme tento kanál zabezpečit.|Komunikace je jedním ze způsobů, jak na portál|

## <a name="select-and-filter-your-metrics"></a>Výběr a filtrování metriky

(K dispozici v ASP.NET, ASP.NET Core a Azure Functions (v2).)

Vlastní klíčový ukazatel výkonu můžete monitorovat tak, že použijete libovolné filtry na jakékoli Application Insights telemetrie z portálu. Klikněte na ovládací prvek filtru, který se zobrazí při přesunutí ukazatele myši na některý z grafů. Následující graf znázorňuje vlastní klíčový ukazatel výkonu počtu požadavků s filtry na atributy adresa URL a doba trvání. Ověřte filtry pomocí části Stream Preview, která zobrazuje živý informační kanál telemetrie, který odpovídá zadaným kritériím v libovolném časovém okamžiku.

![Filtr míry požadavků](./media/live-stream/filter-request.png)

Můžete monitorovat hodnotu odlišnou od počtu. Možnosti závisí na typu datového proudu, což může být jakákoli Application Insights telemetrie: požadavky, závislosti, výjimky, trasování, události nebo metriky. Může to být vlastní [měření](../../azure-monitor/app/api-custom-events-metrics.md#properties):

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

![Ukázka neaktivních selhání](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Filtrovat podle instance serveru

Pokud chcete monitorovat určitou instanci role serveru, můžete filtrovat podle serveru. Chcete-li filtrovat, vyberte název serveru v části *servery*.

![Ukázka neaktivních selhání](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Zabezpečení řídicího kanálu

> [!NOTE]
> V současné době můžete nastavit jenom ověřený kanál pomocí monitorování základních kódů a nemůžete ověřovat servery pomocí připojení bez kódu.

Vlastní kritéria filtrů, která zadáte, se vrátí zpět na komponentu živých metrik v sadě Application Insights SDK. Filtry mohou potenciálně obsahovat citlivé informace, jako jsou například KódZákazníka. Kanál můžete nastavit jako zabezpečený pomocí tajného klíče rozhraní API, a to i pomocí klíče instrumentace.
### <a name="create-an-api-key"></a>Vytvoření klíče rozhraní API

![Klíč rozhraní API > vytvořit klíč rozhraní API – ](./media/live-stream/api-key.png)
 ![ karta vytvořit klíč rozhraní API. Vyberte "ověřit kontrolní kanál SDK" a pak "vygenerovat klíč".](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>Přidat klíč rozhraní API do konfigurace

### <a name="classic-aspnet"></a>Klasický ASP.NET

Do souboru applicationinsights.config přidejte AuthenticationApiKey do QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Nebo v kódu ho nastavte na QuickPulseTelemetryModule:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Aplikace Azure Functions

V případě aplikací Azure Function App (v2) lze zabezpečení kanálu pomocí klíče rozhraní API provést pomocí proměnné prostředí.

Vytvořte klíč rozhraní API z Application Insights prostředku a v **nastavení aplikace** pro Function App použijte. Vyberte **Přidat nové nastavení** a zadejte název `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` a hodnotu, která odpovídá vašemu klíči rozhraní API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-or-greater"></a>ASP.NET Core (vyžaduje sadu Application Insights ASP.NET Core SDK 2.3.0 nebo vyšší)

Upravte soubor startup.cs následujícím způsobem:

Nejprve přidat

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Potom v rámci metody ConfigureServices přidejte:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Pokud ale znáte všechny připojené servery a důvěřujete jim, můžete si vyzkoušet vlastní filtry bez ověřeného kanálu. Tato možnost je k dispozici po dobu šesti měsíců. Toto přepsání se vyžaduje po každé nové relaci, nebo když se nový server dostane do online režimu.

![Možnosti ověřování živých metrik](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Důrazně doporučujeme, abyste nastavili ověřený kanál před zadáním potenciálně citlivých informací, jako je například KódZákazníka v kritériích filtru.
>

## <a name="supported-features-table"></a>Tabulka podporovaných funkcí

| Jazyk                         | Základní metriky       | Metriky výkonu | Vlastní filtrování    | Ukázková telemetrie    | PROCESOR rozdělený podle procesu |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Podporováno (V 2.7.2 +) | Podporováno (V 2.7.2 +) | Podporováno (V 2.7.2 +) | Podporováno (V 2.7.2 +) | Podporováno (V 2.7.2 +)  |
| .NET Core (Target =. NET Framework)| Podporováno (V 2.4.1 +) | Podporováno (V 2.4.1 +) | Podporováno (V 2.4.1 +) | Podporováno (V 2.4.1 +) | Podporováno (V 2.4.1 +)  |
| .NET Core (Target =. NET Core)     | Podporováno (V 2.4.1 +) | Podporuje se*          | Podporováno (V 2.4.1 +) | Podporováno (V 2.4.1 +) | **Nepodporováno**    |
| Azure Functions v2               | Podporováno           | Podporováno           | Podporováno           | Podporováno           | **Nepodporováno**    |
| Java                             | Podporováno (V 2.0.0 +) | Podporováno (V 2.0.0 +) | **Nepodporováno**   | **Nepodporováno**   | **Nepodporováno**    |
| Node.js                          | Podporováno (V 1.3.0 +) | Podporováno (V 1.3.0 +) | **Nepodporováno**   | Podporováno (V 1.3.0 +) | **Nepodporováno**    |

Základní metriky zahrnují četnost požadavků, závislostí a výjimek. Metriky výkonu (čítače výkonu) obsahují paměť a procesor. Ukázková telemetrie zobrazuje datový proud s podrobnými informacemi o neúspěšných požadavcích a závislostech, výjimkách, událostech a trasováních.

 \*Podpora PerfCounters se mírně liší napříč verzemi .NET Core, které necílí na .NET Framework:

- Metriky PerfCounters se podporují při použití v Azure App Service pro Windows. (AspNetCore SDK verze 2.4.1 nebo vyšší)
- PerfCounters se podporují, když je aplikace spuštěná na LIBOVOLNÝch počítačích s Windows (virtuální počítač nebo cloudová služba nebo na Prem atd.). (AspNetCore SDK verze 2.7.1 nebo vyšší), ale pro aplikace cílené na .NET Core 2,0 nebo vyšší.
- PerfCounters se podporují, pokud je aplikace spuštěná kdekoli (Linux, Windows, App Service pro Linux, kontejnery atd.) v nejnovější verzi beta (tj. AspNetCore SDK verze 2.8.0-Beta1 nebo vyšší), ale pro aplikace cílené na .NET Core 2,0 nebo vyšší.

Ve výchozím nastavení jsou aktivní metriky v sadě Node.js SDK zakázané. Pokud chcete povolit živé metriky, přidejte `setSendLiveMetrics(true)` do [metod konfigurace](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) při inicializaci sady SDK.

## <a name="troubleshooting"></a>Řešení potíží

Žádná data? Pokud je vaše aplikace v chráněné síti: Live Metrics Stream používá jiné IP adresy než jiná telemetrie Application Insights. Ujistěte se, že jsou [tyto IP adresy](../../azure-monitor/app/ip-addresses.md) v bráně firewall otevřené.

## <a name="next-steps"></a>Další kroky
* [Monitorování využití pomocí Application Insights](../../azure-monitor/app/usage-overview.md)
* [Pomocí diagnostického vyhledávání](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Snapshot Debugger](../../azure-monitor/app/snapshot-debugger.md)
