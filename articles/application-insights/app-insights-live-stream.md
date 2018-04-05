---
title: Živý datový proud metriky s vlastní metriky a diagnostiky ve službě Azure Application Insights | Microsoft Docs
description: Monitorování webové aplikace v reálném čase s vlastní metriky a diagnostikovat problémy s za provozu informační kanál selhání, trasování a události.
services: application-insights
documentationcenter: ''
author: SoubhagyaDash
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: mbullwin
ms.openlocfilehash: f0338642ab99af2fd5ec4f6432bbb8d626daea29
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Živý datový proud metriky: Diagnostikujte s latencí 1 sekundu & monitorovat 

Probe prezenční signál za provozu, v produkční webové aplikace pomocí živý datový proud metriky z [Application Insights](app-insights-overview.md). Vyberte a filtrovat metriky a výkonu čítače pro sledování v reálném čase, bez narušení k službě. Zkontrolujte trasování zásobníku z ukázkové se nezdařilo požadavky a výjimkami. Společně s [profileru](app-insights-profiler.md), [ladicí program snímku](app-insights-snapshot-debugger.md), a [testování výkonu](app-insights-monitor-web-app-availability.md#performance-tests), živý datový proud metriky poskytuje výkonný a neinvazivní nástroj pro diagnostiku pro váš web za provozu lokalita.

Živý datový proud metriky můžete:

* Ověření opravu, při jeho vydání, pomocí sledování výkonu a selhání počty.
* Sledování účinku testování zatížení a diagnostikovat problémy za provozu. 
* Zaměřit na konkrétní test relací nebo odfiltrovat známých problémů, výběru a filtrování metriku, kterou chcete sledovat.
* Získáte výjimka trasování, při jejich provádění.
* Experimentujte s filtry najít nejdůležitější klíčových ukazatelů výkonu.
* Monitorování oknech výkonu čítač za provozu.
* Snadno Identifikujte server, který má problémy a filtr, který všechny klíčový ukazatel výkonu nebo live kanálu právě daného serveru.

[![Za provozu metriky streamování videa](./media/app-insights-live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Živý datový proud metriky je aktuálně k dispozici pro aplikace ASP.NET z běžících místně nebo v cloudu. 

## <a name="get-started"></a>Začínáme

1. Pokud jste ještě [nenainstalovali Application Insights](app-insights-asp-net.md) ve vaší webové aplikaci ASP.NET nebo [aplikace pro Windows server](app-insights-windows-services.md), udělejte to teď. 
2. **Aktualizace na nejnovější verzi** balíčku Application Insights. V sadě Visual Studio, klikněte pravým tlačítkem na projekt a zvolte **spravovat balíčky Nuget balíčky**. Otevřete **aktualizace** zkontrolujte **zahrnout předběžné verze**a vybrat všechny balíčky Microsoft.ApplicationInsights.*.

    Znovu nasaďte aplikaci.

3. V [portál Azure](https://portal.azure.com), otevřete prostředek Application Insights pro vaši aplikaci a pak otevřete živý datový proud.

4. [Zabezpečený kanál řízení](#secure-the-control-channel) Pokud můžete použít citlivá data, jako jsou jména zákazníků v filtry.


![V okně Přehled klikněte na živý datový proud](./media/app-insights-live-stream/live-stream-2.png)

### <a name="no-data-check-your-server-firewall"></a>Žádná data? Zkontrolujte server brány firewall

Zkontrolujte [Odchozí porty pro živý datový proud metriky](app-insights-ip-addresses.md#outgoing-ports) jsou otevřeny v bráně firewall vašich serverů. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Jak živý datový proud metriky liší od Průzkumníku metrik a analýzy?

| |Live Stream | Průzkumníku metrik a analýzy |
|---|---|---|
|Latence|Data zobrazená v rámci jedné sekundy|Agregován v minutách|
|Žádné uchování|Data udržuje je na graf a potom je zahozen|[Data uchovávat 90 dnů](app-insights-data-retention-privacy.md#how-long-is-the-data-kept)|
|Na vyžádání|Data je streamování při otevření metriky za provozu|Data se odesílají vždy, když je nainstalované a povolené sady SDK|
|Free|Je bezplatná živý datový proud dat|Podléhají [ceny](app-insights-pricing.md)
|Vzorkování|Přenášejí se všechny vybrané metriky a čítače. Chyby a trasování zásobníku jsou odebírána data. TelemetryProcessors se nepoužijí.|Události může být [vzorků](app-insights-api-filtering-sampling.md)|
|Řídicí kanál|Filtr řízení signálů k sadě SDK. Doporučujeme [zabezpečit tento kanál](#secure-channel).|Komunikace je jednosměrný k portálu|


## <a name="select-and-filter-your-metrics"></a>Vyberte a filtrovat vaše metriky

(K dispozici na klasické aplikace ASP.NET s nejnovější sadu SDK).

Vlastní klíčového ukazatele výkonu za provozu můžete monitorovat použitím libovolný filtry na všechny telemetrie Application Insights z portálu. Klikněte na ovládací prvek filtru, který ukazuje, když jste myší nad žádné grafy. Následující graf je vykreslení vlastní počtu žádostí o klíčového ukazatele výkonu s filtry na adresu URL a doba trvání atributy. Ověřte filtry s oddílu Preview datový proud, který ukazuje za provozu informační kanál telemetrická data, která odpovídá kritéria, která jste zadali v libovolném bodě v čase. 

![Vlastní požadavek klíčového ukazatele výkonu](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Hodnota, která se liší od počtu můžete monitorovat. Možnosti závisí na typu datový proud, který může být jakékoli telemetrie Application Insights: požadavky, závislostí, výjimek, trasování, události nebo metriky. Může být vlastní [vlastní měření](app-insights-api-custom-events-metrics.md#properties):

![Hodnota možnosti](./media/app-insights-live-stream/live-stream-valueoptions.png)

Kromě telemetrie Application Insights můžete také sledovat všech čítačů výkonu systému Windows, vyberte jednu z možností datového proudu a poskytnutím název čítače výkonu.

Za provozu metriky agregován na dva body: místně na každém serveru a na všechny servery. Můžete změnit výchozí nastavení v buď pomocí dalších možností v příslušných rozevírací seznamy.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Ukázka Telemetrie: Vlastní za provozu diagnostických událostí
Ve výchozím nastavení za provozu informačního kanálu událostí zobrazuje ukázky neúspěšných požadavků a závislostí volání, výjimky, událostí a trasování. Klikněte na ikonu filtru zobrazit kritéria použitá v libovolném bodě v čase. 

![Výchozí živého kanálu](./media/app-insights-live-stream/live-stream-eventsdefault.png)

Jako s metriky, můžete zadat všechny libovolný kritéria pro jakýkoli z typů telemetrie Application Insights. V tomto příkladu jsme se výběr konkrétního požadavku selhání, trasování a události. Také jsme jsou vyberete všechny výjimky a selhání závislostí.

![Vlastní za provozu informačního kanálu](./media/app-insights-live-stream/live-stream-events.png)

Poznámka: v současné době pro výjimky na základě zpráv kritéria používáte zpráva nejkrajnější výjimky. V předchozím příkladu, filtrovat neškodné výjimky s zpráva o vnitřní výjimce (odpovídá "<--" oddělovač) "klient odpojen." použití zprávu není – obsahuje kritéria pro "Chyba při čtení obsahu žádosti".

Kliknutím zobrazit podrobnosti o položce v za provozu informačního kanálu. Je možné pozastavit informačního kanálu, buď kliknutím **pozastavit** nebo jednoduše posouvání dolů, nebo kliknutím na položku. Za provozu kanálu bude pokračovat po přejděte zpět na horní, nebo klikněte na čítač položek shromážděných během byla pozastavena.

![Vzorkovat selhání za provozu](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrovat podle instance serveru

Pokud chcete monitorovat instanci role konkrétní server, můžete filtrovat podle serveru.

![Vzorkovat selhání za provozu](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Požadavky na sady SDK
Vlastní živý datový proud metriky je k dispozici verze 2.4.0-beta2 nebo novější z [Application Insights SDK pro webové](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Nezapomeňte vybrat možnost "Zahrnout předprodejní verze" ze Správce balíčků NuGet.

## <a name="secure-the-control-channel"></a>Zabezpečený kanál ovládací prvek
Vlastní kritéria filtry, které zadáte jsou odesílány zpět za provozu metriky součástí Application Insights SDK. Filtry mohou obsahovat citlivé informace, jako je například customerIDs. Kanál můžete zabezpečit s tajným klíčem rozhraní API kromě klíč instrumentace.
### <a name="create-an-api-key"></a>Vytvořte klíč rozhraní API

![Vytvořte klíč rozhraní api](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Přidat klíč rozhraní API do konfigurace

# <a name="net-standardtabnet-standard"></a>[.NET Standard](#tab/.net-standard)

V souboru applicationinsights.config soubor přidejte AuthenticationApiKey QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Nebo v kódu, nastavte ji na QuickPulseTelemetryModule:

``` C#

    module.AuthenticationApiKey = "YOUR-API-KEY-HERE";

```
# <a name="net-core-tabnet-core"></a>[.NET core] (# karta/.net jader)

Úprava souboru startup.cs následujícím způsobem:

Nejprve přidat

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;
```

Potom v části Konfigurace metodu přidejte:

``` C#
  QuickPulseTelemetryModule dep;
            var modules = app.ApplicationServices.GetServices<ITelemetryModule>();
            foreach (var module in modules)
            {
                if (module is QuickPulseTelemetryModule)
                {
                    dep = module as QuickPulseTelemetryModule;
                    dep.AuthenticationApiKey = "YOUR-API-KEY-HERE";
                    dep.Initialize(TelemetryConfiguration.Active);
                }
            }
```

---

Pokud znáte a důvěřujete všechny propojené servery, můžete zkusit vlastní filtry bez ověřené kanál. Tato možnost je k dispozici po dobu šesti měsíců. Toto přepsání je požadovaná jednou každou novou relaci, nebo při přechodu do režimu online na nový server.

![Za provozu možnosti ověřování metriky](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>Důrazně doporučujeme, abyste nastavili ověřené kanál před vstupem potenciálně citlivé informace, jako je CustomerID v kritéria filtru.
>

## <a name="generating-a-performance-test-load"></a>Generování zátěžového testu výkonu

Pokud chcete sledovat účinek zvýšení zatížení, použijte okno Test výkonu. Simuluje požadavky od počet současně připojených uživatelů. Můžete ho spustit buď "ruční testy" (ping testy) jedné adresy URL, nebo můžete spustit [vícekrokového webového testu výkonnosti](app-insights-monitor-web-app-availability.md#multi-step-web-tests) nahraného (stejným způsobem jako test dostupnosti).

> [!TIP]
> Po vytvoření testu výkonnosti otevřete test a v okně živý datový proud v samostatném systému windows. Se zobrazí při spuštění testu výkonu zařazených do fronty a sledovat živý datový proud ve stejnou dobu.
>


## <a name="troubleshooting"></a>Řešení potíží

Žádná data? Pokud je aplikace v chráněná síťová: Live Stream metriky používá jinou IP adresy, než ostatní telemetrie Application Insights. Zajistěte, aby [tyto IP adresy](app-insights-ip-addresses.md) jsou otevřeny v bráně firewall.



## <a name="next-steps"></a>Další postup
* [Sledování použití s nástrojem Application Insights](app-insights-web-track-usage.md)
* [Pomocí vyhledávání diagnostiky](app-insights-diagnostic-search.md)
* [Profiler](app-insights-profiler.md)
* [Ladicí program snímku](app-insights-snapshot-debugger.md)
