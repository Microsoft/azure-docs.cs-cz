---
title: Live Metrics Stream vlastními metrikami a diagnostiku ve službě Azure Application Insights | Dokumentace Microsoftu
description: Monitorování webové aplikace v reálném čase s použitím vlastních metrik a diagnostikovat problémy s živého kanálu chyby, trasování a události.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/28/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: f369eb6241a8eb3d44a0a38e243c533da47103e1
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104605"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: Monitorování a Diagnostika s latencí 1 sekundu

Testovat srdce činnost do produkčního prostředí, živé webové aplikace pomocí Live Metrics Stream z [Application Insights](../../azure-monitor/app/app-insights-overview.md). Výběr a filtrování metriky a čítače výkonu ke sledování v reálném čase, bez narušení do vaší služby. Zkontrolujte trasování zásobníku z ukázky se nezdařilo žádosti a výjimky. Spolu s [Profiler](../../azure-monitor/app/profiler.md), [Snapshot debugger](../../azure-monitor/app/snapshot-debugger.md), a [testování výkonu](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests), Live Metrics Stream poskytuje výkonné a neinvazivní diagnostický nástroj pro živé webové lokalita.

Live Metrics Stream můžete:

* Ověření opravy při jejím oficiálním vydání Zhlédnutím počty výkonu a selhání.
* Sledujte účinek testu zatížení a diagnostikovat problémy s live. 
* Zaměřte se na relace určitého testu nebo odfiltrovat známých problémů, výběr a filtrování metriky, které chcete sledovat.
* Získáte trasování výjimky, jak k nim dojde.
* Experimentujte s filtry je možné najít relevantní klíčové ukazatele výkonu.
* Monitorujte všechny Windows výkonu čítač live.
* Snadno Identifikujte problémy a filtr, který všechny klíčový ukazatel výkonu/živého kanálu do právě tento server dochází k danému serveru.

[![Live Metrics Stream video](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Živé metriky jsou aktuálně podporovány pro aplikace ASP.NET, ASP.NET Core, Azure Functions a Java.

## <a name="get-started"></a>Začínáme

1. Pokud jste tak ještě neučinili [nainstalujte službu Application Insights](../../azure-monitor/azure-monitor-app-hub.md) ve webové aplikaci, proveďte to nyní.
2. Kromě standardních balíčků Application Insights [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) povolit Live Metrics stream.
3. **Aktualizace na nejnovější verzi** balíčku Application Insights. V sadě Visual Studio, klikněte pravým tlačítkem na projekt a zvolte **Správa balíčků Nuget**. Otevřít **aktualizace** kartu a vybrat všechny balíčky Microsoft.ApplicationInsights.*.

    Znovu nasaďte aplikaci.

3. V [webu Azure portal](https://portal.azure.com), otevřete prostředek Application Insights pro vaši aplikaci a pak otevřete Live Stream.

4. [Zabezpečený kanál ovládací prvek](#secure-the-control-channel) používáte může být citlivá data, jako jsou jména zákazníků v svoje filtry.

### <a name="no-data-check-your-server-firewall"></a>Žádná data? Zkontrolujte bránu firewall serveru

Zkontrolujte [výchozí porty pro Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) jsou otevřeny v bráně firewall vašich serverů. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Jak Live Metrics Stream se liší od Průzkumníka metrik a Analytics?

| |Live Stream | Průzkumník metrik a Analytics |
|---|---|---|
|Latence|Data zobrazená v rámci jedné sekundy|Agregován v minutách|
|Žádné uchovávání informací|Data nevyřeší, zatímco je v grafu a potom je zahozen|[Data se uchovávají po dobu 90 dnů](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|Na vyžádání|Streamování dat, zatímco otevřete Live Metrics|Data se odesílají pokaždé, když je nainstalován a povolen sady SDK|
|Free|Neexistuje žádné poplatky za data Live Stream|Podléhají [ceny](../../azure-monitor/app/pricing.md)
|Vzorkování|Všechny vybrané metriky a čítače přenosu. Chyby a trasování zásobníku jsou vzorkovány. TelemetryProcessors se nepoužijí.|Události mohou být [vzorkováno](../../azure-monitor/app/api-filtering-sampling.md)|
|Řídicí kanál|Signály ovládací prvek filtru se odesílají do sady SDK. Doporučujeme [zabezpečení v tomto kanálu](#secure-channel).|Komunikace je jednosměrná k portálu|


## <a name="select-and-filter-your-metrics"></a>Výběr a filtrování metriky

(K dispozici s technologií ASP.NET, ASP.NET Core a Azure Functions (v2).)

Vlastní klíčového ukazatele výkonu za provozu můžete monitorovat pomocí použití libovolného filtrů na žádnou telemetrii Application Insights z portálu. Klikněte na ovládací prvek filtru, který ukazuje, kdy jste myší nad žádné grafy. V následující tabulce je vykreslení vlastní počet požadavků klíčový ukazatel výkonu s filtry pro adresu URL a doba trvání atributy. Ověřte vaše filtry s oddílem Stream ve verzi Preview, která zobrazuje živého kanálu telemetrických dat, která by odpovídala kritériím, která jste zadali v libovolném bodě v čase. 

![Vlastní požadavek klíčového ukazatele výkonu](./media/live-stream/live-stream-filteredMetric.png)

Hodnota, která se liší od počtu můžete monitorovat. Možnosti závisí na typu datový proud, který může být žádnou telemetrii Application Insights: žádosti, závislosti, výjimky, trasování, události nebo metriky. Může být vlastní [vlastní měření](../../azure-monitor/app/api-custom-events-metrics.md#properties):

![Hodnota možnosti](./media/live-stream/live-stream-valueoptions.png)

Kromě telemetrie Application Insights můžete také monitorovat všechny čítače výkonu Windows výběrem, který z možnosti datového proudu a poskytnutí názvu čítače výkonu.

Živé metriky se agregují na dvou místech: místně na každém serveru a na všech serverech. Můžete změnit výchozí nastavení v buď výběrem jiné možnosti v příslušné rozevírací seznamy.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Ukázková telemetrická data: Vlastní Live diagnostické události
Ve výchozím nastavení zobrazuje živého kanálu událostí ukázky neúspěšných požadavků a volání závislosti, výjimky, události a trasování. Kliknutím na ikonu filtru zobrazíte použité kritéria v libovolném bodě v čase. 

![Výchozí živého kanálu](./media/live-stream/live-stream-eventsdefault.png)

Jako s metrikami, můžete zadat libovolný libovolného kritéria pro některé typy telemetrie Application Insights. V tomto příkladu volíme konkrétního požadavku chyby, trasování a události. Také volíme všechny výjimky a chyby závislostí.

![Vlastní živého kanálu](./media/live-stream/live-stream-events.png)

Poznámka: V současné době pro výjimku pomocí založenou na zprávách kritérií, zpráva o vnější výjimce. V předchozím příkladu, k filtrování neškodné výjimky se zpráva o vnitřní výjimce (následuje "<--" oddělovač) "Klient byl odpojen." použití zprávu not obsahuje kritéria "Chyba při čtení obsahu žádosti".

Kliknutím zobrazit podrobnosti položky v živého kanálu. Je možné pozastavit informačního kanálu, po kliknutí buď **pozastavit** nebo jednoduše procházet nebo kliknutím na položku. Živého kanálu bude pokračovat, až přejděte zpět do horní části nebo kliknutím Čítač položek shromážděných během byla pozastavena.

![Vzorkováno živé selhání](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrovat podle instance serveru

Pokud chcete monitorovat instance role konkrétní server, můžete filtrovat podle serveru.

![Vzorkováno živé selhání](./media/live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Požadavky na sadu SDK
Vlastní Live Metrics Stream je k dispozici s verzí 2.4.0-beta2 nebo novější z [Application Insights SDK pro webové](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Nezapomeňte vybrat možnost "Zahrnout předprodejní verze" ze Správce balíčků NuGet.

## <a name="secure-the-control-channel"></a>Zabezpečený kanál ovládacího prvku
Vlastní kritéria filtry, které jste zadali odesílají zpět do komponenty Live Metrics v Application Insights SDK. Filtry můžou potenciálně obsahovat citlivé informace, jako je například customerIDs. Kanál můžete zabezpečit pomocí tajného klíče rozhraní API kromě Instrumentační klíč.
### <a name="create-an-api-key"></a>Vytvořte klíč rozhraní API

![Vytvořit klíč rozhraní API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Klíč rozhraní API přidejte do konfigurace

### <a name="classic-aspnet"></a>Klasické ASP.NET

V souboru applicationinsights.config přidejte AuthenticationApiKey QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Nebo v kódu, nastavte ho na QuickPulseTelemetryModule:

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

### <a name="azure-function-apps"></a>Aplikace Azure Function App

Pro zabezpečení kanálu pomocí rozhraní API aplikace Azure Function (v2) klíč můžete provést pomocí proměnné prostředí. 

Vytvořte klíč rozhraní API z v rámci prostředku Application Insights a přejděte na **nastavení aplikace** pro vaši aplikaci Function App. Vyberte **přidat nové nastavení** a zadejte název `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` a hodnotu, která odpovídá vašemu klíči rozhraní API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (2.3.0-beta vyžaduje ASP.NET Core sadu SDK Application Insights nebo vyšší)

Upravte soubor startup.cs následujícím způsobem:

Nejprve přidat

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Pak v rámci metody ConfigureServices přidejte:

``` C#
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Pokud znáte a důvěřujete všechny propojené servery, můžete zkusit vlastní filtry bez ověření kanálu. Tato možnost je k dispozici po dobu šesti měsíců. Toto přepsání je požadovaná jednou každých novou relaci, nebo když nový server převede do režimu online.

![Volby živé metriky ověřování](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Důrazně doporučujeme, abyste nastavili ověřeného kanál před zadáním potenciálně citlivé informace, jako je ID zákazníka v podmínce filtru.
>

## <a name="generating-a-performance-test-load"></a>Generování zatížení test výkonu

Pokud chcete sledovat účinek zvýšení zátěže, použijte okno Test výkonnosti. Simuluje žádosti z několika souběžných uživatelů. Je možné spustit buď "manuálních testů" (ping testy) jedné adresy URL, nebo ji můžete spustit [vícekrokový webový test výkonnosti](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) , který nahrajete (stejným způsobem jako test dostupnosti).

> [!TIP]
> Po vytvoření testu výkonnosti otevřete test a okno Live Stream v samostatném systému windows. Zobrazí se při spuštění testu výkonnosti zařazených do fronty a sledovat živý stream ve stejnou dobu.
>


## <a name="troubleshooting"></a>Řešení potíží

Žádná data? Pokud je vaše aplikace v chráněné síti: Live Metrics Stream používá jiný IP adres než jiné telemetrie Application Insights. Ujistěte se, že [tyto IP adresy](../../azure-monitor/app/ip-addresses.md) jsou otevřeny v bráně firewall.



## <a name="next-steps"></a>Další postup
* [Sledování využití pomocí Application Insights](../../azure-monitor/app/usage-overview.md)
* [Pomocí vyhledávání diagnostiky](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Ladicí program snímků](../../azure-monitor/app/snapshot-debugger.md)
