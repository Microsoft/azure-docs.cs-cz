---
title: Diagnostika živého datového proudu metrik – přehledy aplikací Azure
description: Sledujte svou webovou aplikaci v reálném čase pomocí vlastních metrik a diagnostikujte problémy pomocí živého přenosu selhání, trasování a událostí.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: ea0d786d0b8b96941d791bcc8e92fad9a869c5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670096"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Stream živých metrik: Monitor & diagnostikovat s latencí 1 sekundy

Sondujte tlukoucí srdce vaší živé, produkční webové aplikace pomocí živého streamování metrik z [Application Insights](../../azure-monitor/app/app-insights-overview.md). Vyberte a filtrujte metriky a čítače výkonu, které chcete sledovat v reálném čase, bez jakéhokoli narušení vaší služby. Zkontrolujte trasování zásobníku z ukázkových neúspěšných požadavků a výjimek. Spolu s [Profiler](../../azure-monitor/app/profiler.md), [snímek ladicí program](../../azure-monitor/app/snapshot-debugger.md). Live Metrics Stream poskytuje výkonný a neinvazivní diagnostický nástroj pro vaše živé webové stránky.

S živým streamováním metrik můžete:

* Ověřte opravu během vydání sledováním výkonu a počtu selhání.
* Sledujte účinek testovacích zatížení a diagnostikujte problémy živě.
* Zaměřte se na konkrétní testovací relace nebo odfiltrujte známé problémy výběrem a filtrováním metrik, které chcete sledovat.
* Získejte trasování výjimek, jak k nim dochází.
* Experimentujte s filtry a najděte nejrelevantnější hlavní sady klíčů.
* Sledujte všechny čítače výkonu systému Windows v přímém přenosu.
* Snadno identifikujte server, který má problémy, a filtrujte veškerý klíčový ukazatel výkonu/živý přenos pouze na tento server.

[![Video streamu živých metrik](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Živé metriky jsou momentálně podporované pro aplikace ASP.NET, ASP.NET Core, Azure Functions, Java a Node.js.

## <a name="get-started"></a>Začínáme

1. Pokud jste ještě [nenainstalovali Application Insights](../../azure-monitor/azure-monitor-app-hub.yml) ve webové aplikaci, udělejte to teď.
2. Kromě standardních balíčků Application Insights [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) je nutné povolit živé metriky datového proudu.
3. **Aktualizujte na nejnovější verzi** balíčku Application Insights. V sadě Visual Studio klikněte pravým tlačítkem myši na projekt a zvolte **Spravovat balíčky Nuget**. Otevřete kartu **Aktualizace** a vyberte všechny balíčky Microsoft.ApplicationInsights.*.

    Znovu nasaďte aplikaci.

3. Na [webu Azure Portal](https://portal.azure.com)otevřete prostředek Application Insights pro vaši aplikaci a pak otevřete Live Stream.

4. [Zabezpečte řídicí kanál,](#secure-the-control-channel) pokud můžete ve filtrech používat citlivá data, jako jsou jména zákazníků.

### <a name="no-data-check-your-server-firewall"></a>Žádná data? Kontrola brány firewall serveru

Podívejte se, [zda jsou odchozí porty pro Živé metriky Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) otevřené v bráně firewall vašich serverů.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Jak se živý přenos metrik liší od Průzkumníka metrik a analýzy?

| |Live Stream | Průzkumník metrik a analýza |
|---|---|---|
|Latence|Data zobrazená během jedné sekundy|Agregované za minuty|
|Bez uchovávání informací|Data zůstanou zachována, když jsou v grafu, a poté se zahodí.|[Údaje uchovávané po dobu 90 dnů](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|Na vyžádání|Data se streamují při otevření živých metrik|Data jsou odesílána vždy, když je sada SDK nainstalována a povolena|
|Free|Za data živého přenosu se neplatí|S výhradou [stanovení cen](../../azure-monitor/app/pricing.md)
|Vzorkování|Všechny vybrané metriky a čítače jsou přenášeny. Chyby a trasování zásobníku jsou vzorkovány. TelemetryProcesory nejsou použity.|Události mohou být [vzorkovány](../../azure-monitor/app/api-filtering-sampling.md)|
|Ovládací kanál|Signály řízení filtru jsou odesílány do sady SDK. Doporučujeme zabezpečit tento kanál.|Komunikace je jedním ze způsobů, jak na portál|

## <a name="select-and-filter-your-metrics"></a>Výběr a filtrování metrik

(K dispozici s ASP.NET, ASP.NET jádra a funkce Azure (v2).)

Vlastní klíčový ukazatel výkonu můžete sledovat živě použitím libovolných filtrů na libovolné telemetrii Application Insights z portálu. Klikněte na ovládací prvek filtru, který se zobrazí, když najedete myší na některý z grafů. Následující graf vykresluje vlastní klíčový ukazatel výkonu počtu požadavků s filtry na atributech URL a Duration. Ověřte filtry pomocí části Náhled datového proudu, která zobrazuje živý přenos telemetrie, který odpovídá kritériím, která jste zadali v libovolném okamžiku.

![Klíčový ukazatel výkonu vlastního požadavku](./media/live-stream/live-stream-filteredMetric.png)

Můžete sledovat hodnotu jinou než Count. Možnosti závisí na typu datového proudu, což může být jakákoli telemetrie Application Insights: požadavky, závislosti, výjimky, trasování, události nebo metriky. Může to být vaše [vlastní měření:](../../azure-monitor/app/api-custom-events-metrics.md#properties)

![Možnosti pro hodnoty](./media/live-stream/live-stream-valueoptions.png)

Kromě telemetrie Application Insights můžete také sledovat libovolný čítač výkonu systému Windows výběrem z možností datového proudu a zadáním názvu čítače výkonu.

Živé metriky jsou agregovány ve dvou bodech: místně na každém serveru a potom na všech serverech. Výchozí nastavení můžete změnit buď výběrem dalších možností v příslušných rozevíracích položce.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Ukázková telemetrie: Vlastní živé diagnostické události
Ve výchozím nastavení zobrazuje živý přenos událostí ukázky neúspěšných požadavků a volání závislostí, výjimek, událostí a trasování. Kliknutím na ikonu filtru zobrazíte použitá kritéria v libovolném okamžiku. 

![Výchozí živý přenos](./media/live-stream/live-stream-eventsdefault.png)

Stejně jako u metriky můžete zadat libovolná kritéria pro libovolný typ telemetrie Application Insights. V tomto příkladu vybíráme konkrétní selhání požadavků, trasování a události. Také vybíráme všechny výjimky a selhání závislostí.

![Vlastní živý přenos](./media/live-stream/live-stream-events.png)

Poznámka: V současné době pro kritéria založená na výjimce použijte zprávu o nejkrajnější výjimce. V předchozím příkladu odfiltrovat neškodnou výjimku se zprávou vnitřní výjimky (následuje oddělovač "<--" "Klient byl odpojen." použijte zprávu, která neobsahuje kritéria "Chyba při čtení obsahu požadavku".

Kliknutím na položku zobrazíte podrobnosti o položce v živém přenosu. Informační kanál můžete pozastavit buď kliknutím na **Pozastavit** nebo jednoduše posunutím dolů nebo kliknutím na položku. Živý přenos bude pokračovat po přejděte zpět na vrchol, nebo kliknutím na počítadlo položek shromážděných, zatímco to bylo pozastaveno.

![Ukázkové živé poruchy](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrovat podle instance serveru

Pokud chcete sledovat konkrétní instanci role serveru, můžete filtrovat podle serveru.

![Ukázkové živé poruchy](./media/live-stream/live-stream-filter.png)

## <a name="secure-the-control-channel"></a>Zabezpečte řídicí kanál
Zadaná vlastní kritéria filtrů jsou odeslána zpět do komponenty Živé metriky v sadě SDK Application Insights. Filtry mohou potenciálně obsahovat citlivé informace, jako jsou id zákazníka. Kromě klíče instrumentace můžete kanál zabezpečit pomocí tajného klíče rozhraní API.
### <a name="create-an-api-key"></a>Vytvoření klíče rozhraní API

![Vytvořit klíč rozhraní API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Přidat klíč rozhraní API do konfigurace

### <a name="classic-aspnet"></a>Klasické ASP.NET

V souboru applicationinsights.config přidejte hodnotu AuthenticationApiKey do modulu QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Nebo v kódu, nastavte jej na QuickPulseTelemetryModule:

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

Pro aplikace azure funkce (v2), zabezpečení kanálu pomocí klíče rozhraní API lze provést pomocí proměnné prostředí.

Vytvořte klíč rozhraní API z prostředku Application Insights a přejděte do **nastavení aplikace** pro vaši aplikaci funkce. Vyberte **přidat nové nastavení** `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` a zadejte název a hodnotu, která odpovídá klíči rozhraní API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-or-greater"></a>ASP.NET Jádro (vyžaduje přehledy aplikací ASP.NET základní sady SDK 2.3.0 nebo vyšší)

Upravte soubor startup.cs následujícím způsobem:

První přidání

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Potom v rámci ConfigureServices metoda přidat:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Pokud však poznáte a důvěřujete všem připojeným serverům, můžete vyzkoušet vlastní filtry bez ověřeného kanálu. Tato možnost je k dispozici po dobu šesti měsíců. Toto přepsání je vyžadováno při každé nové relaci nebo při přepychu nového serveru.

![Možnosti auth živých metrik](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Důrazně doporučujeme nastavit ověřený kanál před zadáním potenciálně citlivých informací, jako je CustomerID v kritériích filtru.
>

## <a name="supported-features-table"></a>Tabulka podporovaných funkcí

| Jazyk                         | Základní metriky       | Metriky výkonu | Vlastní filtrování    | Ukázková telemetrie    | Rozdělení PROCESORU podle procesu |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Podporováno (V2.7.2+) | Podporováno (V2.7.2+) | Podporováno (V2.7.2+) | Podporováno (V2.7.2+) | Podporováno (V2.7.2+)  |
| Jádro rozhraní .NET (target=.NET Framework)| Podporováno (V2.4.1+) | Podporováno (V2.4.1+) | Podporováno (V2.4.1+) | Podporováno (V2.4.1+) | Podporováno (V2.4.1+)  |
| Jádro .NET (target=.NET Core)     | Podporováno (V2.4.1+) | Podporuje se*          | Podporováno (V2.4.1+) | Podporováno (V2.4.1+) | **Nepodporuje se**    |
| Funkce Azure v2               | Podporuje se           | Podporuje se           | Podporuje se           | Podporuje se           | **Nepodporuje se**    |
| Java                             | Podporováno (V2.0.0+) | Podporováno (V2.0.0+) | **Nepodporuje se**   | **Nepodporuje se**   | **Nepodporuje se**    |
| Node.js                          | Podporováno (V1.3.0+) | Podporováno (V1.3.0+) | **Nepodporuje se**   | Podporováno (V1.3.0+) | **Nepodporuje se**    |

Základní metriky zahrnují požadavek, závislost a míru výjimek. Metriky výkonu (čítače výkonu) zahrnují paměť a procesor. Ukázková telemetrie zobrazuje proud podrobných informací o neúspěšných požadavcích a závislostech, výjimkách, událostech a trasování.

 \*Podpora PerfCounters se mírně liší mezi verzemi .NET Core, které necílí na rozhraní .NET Framework:

- Metriky PerfCounters jsou podporované při spuštění ve službě Azure App Service pro Windows. (AspNetCore SDK verze 2.4.1 nebo vyšší)
- PerfCounters jsou podporované, když aplikace běží v počítačích se systémem Windows (VM nebo cloudové služby nebo On-prem atd.) (AspNetCore SDK verze 2.7.1 nebo vyšší), ale pro aplikace cílení .NET Core 2.0 nebo vyšší.
- PerfCounters jsou podporovány, když aplikace běží ANYWHERE (Linux, Windows, app service pro Linux, kontejnery, atd.) v nejnovější beta verzi (tj. AspNetCore SDK verze 2.8.0-beta1 nebo vyšší), ale pro aplikace cílení .NET Core 2.0 nebo vyšší.

Ve výchozím nastavení je v sadě SDK Node.js zakázána sada Live Metrics. Chcete-li povolit `setSendLiveMetrics(true)` živé metriky, přidejte do [konfiguračních metod](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) při inicializaci sady SDK.

## <a name="troubleshooting"></a>Řešení potíží

Žádná data? Pokud je vaše aplikace v chráněné síti: Live Metrics Stream používá jiné IP adresy než jiné telemetrie Application Insights. Ujistěte [se,](../../azure-monitor/app/ip-addresses.md) že tyto IP adresy jsou otevřené ve vaší bráně firewall.

## <a name="next-steps"></a>Další kroky
* [Sledování využití pomocí přehledů aplikací](../../azure-monitor/app/usage-overview.md)
* [Použití diagnostického vyhledávání](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Ladicí program snímků](../../azure-monitor/app/snapshot-debugger.md)
