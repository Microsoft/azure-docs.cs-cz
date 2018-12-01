---
title: Application Insights pro Azure Cloud Services | Dokumentace Microsoftu
description: Efektivní sledování webových rolí a rolí pracovních procesů s využitím Application Insights
services: application-insights
documentationcenter: ''
keywords: WAD2AI, diagnostika Azure
author: mrbullwinkle
manager: carmonm
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.workload: tbd
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: c39cb01f1477e51623b02941bae03a51a389e197
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682862"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights pro Azure Cloud Services
U [aplikací cloudových služeb Microsoft Azure](https://azure.microsoft.com/services/cloud-services/) je možné pomocí služby [Application Insights][start] monitorovat dostupnost, výkon, chyby a využití díky kombinování dat ze sad SDK služby Application Insights a dat [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) z vašich cloudových služeb. Na základě zpětné vazby ohledně výkonu a efektivity vaší aplikace při běžném používání můžete informovaně rozhodovat o směrování návrhu v každé fázi vývoje.

![Snímek obrazovky řídicího panelu s přehledem](./media/app-insights-cloudservices/overview-graphs.png)

## <a name="before-you-start"></a>Než začnete
Budete potřebovat:

* Předplatné [Microsoft Azure](http://azure.com). Přihlaste se pomocí účtu Microsoft, který můžete mít zřízen pro Windows, XBox Live nebo jiné cloudové služby Microsoftu. 
* Nástroje Microsoft Azure 2.9 nebo novější
* Developer Analytics Tools 7.10 nebo novější

## <a name="quick-start"></a>Rychlý start
Nejrychlejším a nejjednodušším způsobem, jak monitorovat cloudovou službu s použitím Application Insights, je zvolit tuto možnost při publikování služby v Azure.

![Příklad:](./media/app-insights-cloudservices/azure-cloud-application-insights.png)

Tato možnost používá vaši aplikaci za běhu a poskytuje vám veškerá telemetrická data, která potřebujete k monitorování požadavků, výjimek a závislostí v rámci webové role a také čítačů výkonu z rolí pracovních procesů. Veškerá diagnostická trasování vygenerovaná aplikací se také odesílají do Application Insights.

Pokud to je všechno, co potřebujete, jste hotovi! Dalšími kroky jsou [zobrazení metrik z aplikace](app-insights-metrics-explorer.md), [zadávání dotazů na data pomocí Analytics](app-insights-analytics.md) a případně i nastavení [řídicího panelu](app-insights-dashboards.md). Může být vhodné vytvořit [testy dostupnosti](app-insights-monitor-web-app-availability.md) a [přidat do webových stránek kód](app-insights-javascript.md) pro monitorování výkonu v prohlížeči.

Můžete ovšem využívat i další možnosti:

* Odesílání dat z různých komponent a sestavování konfigurací pro různé prostředky.
* Přidání vlastní telemetrie ze své aplikace.

Pokud vás tyto možnosti zajímají, čtěte dál.

## <a name="sample-application-instrumented-with-application-insights"></a>Ukázková aplikace používaná s Application Insights
Podívejte se na tuto [ukázkovou aplikaci](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), ve které je Application Insights přidáno do cloudové služby se dvěma rolemi pracovních procesů hostovanými v Azure. 

Dále je popsáno, jak stejným způsobem upravit vlastní projekt cloudové služby.

## <a name="plan-resources-and-resource-groups"></a>Plánování prostředků a skupin prostředků
Telemetrie z vaší aplikace se ukládá, analyzuje a zobrazuje v prostředku Azure typu Application Insights. 

Každý prostředek patří do skupiny prostředků. Skupiny prostředků se používají ke správě nákladů, k udělování přístupu členům týmu a k nasazování aktualizací v rámci jedné koordinované transakce. Můžete například [napsat skript pro nasazení](../azure-resource-manager/resource-group-template-deploy.md) cloudové služby Azure a příslušných prostředků monitorování Application Insights v rámci jedné operace.

### <a name="resources-for-components"></a>Prostředky pro komponenty
Doporučeným schématem je vytvoření samostatného prostředku pro každou komponentu aplikace – tj. každou webovou roli a roli pracovního procesu. Jednotlivé komponenty můžete analyzovat samostatně, ale můžete také vytvořit [řídicí panel](app-insights-dashboards.md), na kterém se zobrazí klíčové grafy ze všech komponent tak, abyste je mohli porovnávat a monitorovat společně. 

Alternativním schématem je odesílání telemetrických dat z více než jedné role do stejného prostředku, ovšem s [přidáním vlastnosti dimenze pro každou položku telemetrie](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer), aby byla určena její zdrojová role. V tomto schématu se v metrikách grafů, jako jsou například výjimky, normálně zobrazuje agregace počtů z různých rolí, ale v případě potřeby můžete graf rozdělit do segmentů podle identifikátoru role. Podle stejné dimenze je také možné filtrovat hledání. Tato alternativa trochu zjednodušuje zobrazení všech dat současně, může ale vést i k nejasnostem mezi rolemi.

Telemetrická data prohlížeče jsou obvykle součástí stejného prostředku jako u příslušné serverové webové role.

Prostředky Application Insights pro různé součásti převeďte do jedné skupiny prostředků. Pak je budete moct snadno spravovat společně. 

### <a name="separating-development-test-and-production"></a>Oddělení vývoje, testování a provozu
Pokud pro další funkci vyvíjíte vlastní událostí a předchozí verze běží v živém provozu, je vhodné odesílat telemetrická data pro vývoj do samostatného prostředku Application Insights. V opačném případě bude obtížné najít ve veškerém provozu z živě provozovaného webu testovací telemetrická data.

Chcete-li této situaci předejít, vytvořte samostatné prostředky pro každou konfiguraci sestavení nebo pro svůj systém použijte označení (vývoj, test, provoz, ...). Prostředky pro každou konfiguraci sestavení umístěte do samostatné skupiny prostředků. 

Pokud chcete odesílat telemetrická data do příslušných prostředků, můžete nastavit sadu SDK Application Insights tak, aby v závislosti na konfiguraci sestavení použila jiný instrumentační klíč. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Vytvoření prostředku Application Insights pro každou roli
Pokud jste se rozhodli vytvořit samostatný prostředek pro každou roli (a možná samostatnou sadu pro každé sestavení konfigurace), je nejjednodušší vytvořit je všechny na portálu Application Insights. (Pokud vytváříte mnoho prostředků, můžete [proces automatizovat](app-insights-powershell.md).)

1. Na webu [Azure Portal][portal] vytvořte nový prostředek Application Insights. Jako typ aplikace vyberte aplikaci ASP.NET. 

    ![Klikněte na tlačítko Nový, Application Insights](./media/app-insights-cloudservices/01-new.png)
2. Každý prostředek je identifikován instrumentačním klíčem. Je možné, že ho budete potřebovat později, pokud budete chtít ručně konfigurovat sadu SDK nebo její konfiguraci ověřit.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Nastavení diagnostiky Azure pro každou roli
Tuto možnost nastavte, pokud chcete aplikaci monitorovat pomocí Application Insights. V případě webových rolí tento postup umožňuje monitorování, výstrahy a diagnostiku výkonu a také analýzu využití. V případě jiných rolí můžete vyhledávat a monitorovat diagnostiku Azure, jako jsou restarty, čítače výkonu a volání System.Diagnostics.Trace. 

1. V Průzkumníku řešení sady Visual Studio v části &lt;vaše_cloudová_služba&gt;, Role otevřete vlastnosti jednotlivých rolí.
2. V části **Konfigurace**, nastavte možnost **Posílat diagnostická data do Application Insights** a vyberte odpovídající prostředek Application Insights, který jste vytvořili dříve.

Pokud jste se rozhodli použít samostatný prostředek Application Insights pro každou konfiguraci sestavení, vyberte nejprve konfiguraci.

![Ve vlastnostech každé role Azure konfigurujte Application Insights.](./media/app-insights-cloudservices/configure-azure-diagnostics.png)

Tento postup má za následek vložení instrumentačních klíčů Application Insights do souborů s názvy `ServiceConfiguration.*.cscfg`. ([Vzorový kód](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).)

Pokud chcete měnit úroveň diagnostických informací odesílaných do Application Insights, můžete tak učinit [přímou úpravou souborů `.cscfg`](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

## <a name="sdk"></a>Instalace sady SDK v každém projektu
Tato možnost umožňuje přidat vlastní obchodní telemetrická data do kterékoli role kvůli podrobnější analýze způsobu použití aplikace a její výkonnosti.

V sadě Visual Studio nakonfigurujte sadu SDK Application Insights pro každý projekt cloudové aplikace.

1. **Webové role:** Klikněte na projekt pravým tlačítkem myši a zvolte možnost **Nakonfigurovat Application Insights** nebo **Přidat &gt; Telemetrie Application Insights**.

2. **Role pracovních procesů**: 
 * Klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.
 * Přidejte balíček [Application Insights pro servery Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Vyhledejte „Application Insights“](./media/app-insights-cloudservices/04-ai-nuget.png)

3. Konfigurujte sadu SDK tak, aby odesílala data do prostředku Application Insights.

    Ve vhodné spouštěcí funkci nastavte Instrumentační klíč podle nastavení konfigurace v ``.cscfg file``:
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    Proveďte tento postup pro každou roli v aplikaci. Podívejte se na příklady:
   
   * [Webová role](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
   * [Role pracovního procesu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
   * [Pro webové stránky](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 
4. Nastavte soubor ApplicationInsights.config tak, aby se vždy kopíroval do výstupního adresáře. 
   
    (V souboru .config se zobrazí zprávy s výzvou, abyste tam umístili instrumentační klíč. Pro cloudové aplikace je však lepší provést nastavení ze ``.cscfg file``. Zajistí se tím správná identifikace role v portálu.)

#### <a name="run-and-publish-the-app"></a>Spuštění a publikování aplikace
Spusťte aplikaci a přihlaste se k Azure. Otevřete prostředky Application Insights, které jste vytvořili. V části [Vyhledávání](app-insights-diagnostic-search.md) se zobrazí jednotlivé datové body a v části [Průzkumník metrik](app-insights-metrics-explorer.md) se zobrazí agregovaná data. 

Přidejte další telemetrická data (viz části níže) a pak aplikaci publikujte, abyste mohli přijímat zpětnou vazbu ohledně diagnostiky a využití v živém provozu. 

#### <a name="no-data"></a>Žádná data?
* Otevřete dlaždici [Vyhledávání][diagnostic] a zobrazte jednotlivé události.
* Použijte aplikaci a otevřete různé stránky tak, aby došlo k vygenerování nějaké telemetrie.
* Počkejte několik sekund a klikněte na možnost Aktualizovat.
* Další informace najdete v tématu [Poradce při potížích][qna].

## <a name="view-azure-diagnostic-events"></a>Zobrazení událostí diagnostiky Azure
Kde najít informace [diagnostiky Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) ve službě Application Insights:

* Čítače výkonu se zobrazují jako vlastní metriky. 
* Protokoly událostí systému Windows se zobrazují jako trasování a vlastní události.
* Protokoly aplikací, protokoly trasování událostí pro Windows a veškeré protokoly infrastruktury diagnostiky se zobrazují jako trasování.

Chcete-li zobrazit čítače výkonu a počty událostí, otevřete [Průzkumníka metrik](app-insights-metrics-explorer.md) a přidejte nový graf:

![Diagnostická data Azure](./media/app-insights-cloudservices/23-wad.png)

V části [Vyhledávání](app-insights-diagnostic-search.md) nebo [Dotaz Analytics](../log-analytics/query-language/get-started-analytics-portal.md) můžete hledat v různých protokolech trasování odeslaných diagnostikou Azure. Předpokládejme například, že se vyskytla neošetřená výjimka, která způsobila zhroucení a recyklaci role. Tyto informace se zobrazují v kanálu Aplikace protokolu událostí systému Windows. Prostřednictvím hledání můžete najít chybu v protokolu událostí systému Windows a získat úplné trasování zásobníku pro příslušnou výjimku. To vám umožní najít původní příčinu problému.

![Hledání v diagnostice Azure](./media/app-insights-cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Další telemetrická data
Následující části popisují, jak získávat další telemetrická data z různých aspektů aplikace.

## <a name="track-requests-from-worker-roles"></a>Sledování požadavků z rolí pracovních procesů
Ve webových rolí modul požadavků automaticky shromažďuje data týkající se požadavků HTTP. V [ukázce MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole) najdete příklady možností potlačení výchozího chování při shromažďování. 

Můžete zaznamenávat výkon volání rolí pracovních procesů, pokud je budete sledovat stejným způsobem jako požadavky HTTP. V Application Insights typ telemetrických dat Požadavek měří uvedenou jednotku práce serveru, kterou lze časovat a která může nezávisle být úspěšná nebo neúspěšná. Zatímco požadavky HTTP automaticky zachycuje sada SDK, můžete vložit vlastní kód pro sledování požadavků na role pracovních procesů.

Prohlédněte si dvě ukázkové role pracovních procesů, které mají zaznamenávat požadavky: [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) a [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Výjimky
V tématu [Monitorování výjimek v Application Insights](app-insights-asp-net-exceptions.md) najdete informace o tom, jak shromažďovat neošetřené výjimky z různých typů webových aplikací.

Ukázková webová role obsahuje kontrolery rozhraní MVC5 a Web API 2. Nezpracované výjimky z nich jsou zachyceny pomocí následujících obslužných rutin:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) nastavená [zde](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) pro kontrolery rozhraní MVC5
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) nastavená [sem](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) pro kontrolery rozhraní Web API 2

V případě rolí pracovních procesů existují dva způsoby, jak sledovat výjimky:

* TrackException(ex)
* Pokud jste přidali balíček NuGet pro naslouchací proces trasování Application Insights, můžete k protokolování výjimek použít volání **System.Diagnostics.Trace**. [Příklad kódu.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## <a name="performance-counters"></a>Čítače výkonu
Ve výchozím nastavení se shromažďují následující čítače:

    * \Process(??APP_WIN32_PROC??)\% Processor Time
    * \Memory\Available Bytes
    * \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
    * \Process(??APP_WIN32_PROC??)\Private Bytes
    * \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
    * \Processor(_Total)\% Processor Time

Pro webové role se shromažďují i tyto čítače:

    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Další vlastní čítače výkonu nebo jiné čítače výkonu Windows můžete určit provedením úpravy souboru ApplicationInsights.config [jako v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Čítače výkonu](./media/app-insights-cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Korelační telemetrická data pro role pracovních procesů
Díky informačně bohaté diagnostice můžete zjistit, co vedlo k neúspěšnému požadavku nebo k požadavku s vysokou latencí. V případě webových rolí sada SDK automaticky zjišťuje korelaci mezi souvisejícími telemetrickými daty. V případě rolí pracovních procesů můžete použít inicializátor vlastní telemetrie a nastavit společný atribut kontextu Operation.Id pro veškerá telemetrická data, abyste tohoto cíle dosáhli. Díky tomu můžete na první pohled zjistit, jestli byl problém týkající se latence nebo selhání způsoben závislostí na vašem kódu! 

Zde je uveden postup:

* Nastavte ID korelace v CallContext tak, jak je uvedeno [zde](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). V tomto případě používáme jako ID korelace ID požadavku.
* Přidejte vlastní implementaci TelemetryInitializer a nastavte Operation.Id na ID korelace uvedené výše. Příklad je zde: [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* Přidejte inicializátor vlastní telemetrie. Můžete to udělat v souboru ApplicationInsights.config nebo v kódu jak je znázorněno [tady](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetrická data klienta
Po [přidání sady SDK JavaScript do webových stránek][client] můžete získávat telemetrická data související s prohlížečem, jako jsou počty zobrazení stránek, časy načítání stránek nebo výjimky skriptů, a zapisovat ve svých skriptech stránek vlastní telemetrická data.

## <a name="availability-tests"></a>Testy dostupnosti
[Nastavení webových testů][availability] pro zajištění, že aplikace zůstane funkční a bude reagovat.

## <a name="display-everything-together"></a>Zobrazení všeho najednou
Chcete-li získat celkový přehled o systému, můžete klíčové grafy monitorování převést na jeden [řídicí panel](app-insights-dashboards.md). Můžete například připnout počty požadavků a selhání pro jednotlivé role. 

Pokud váš systém využívá jiné služby Azure, například Stream Analytics, jsou zahrnuty i jejich grafy monitorování. 

Pokud máte mobilní klientskou aplikaci, použijte [App Center](app-insights-mobile-center-quickstart.md). V [Analytics](app-insights-analytics.md) můžete vytvářet dotazy pro zobrazení počtu událostí a můžete je připnout na řídicí panel.

## <a name="example"></a>Příklad:
V [příkladu](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) se monitoruje služba s webovou rolí a dvěma rolemi pracovních procesů.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Výjimka „metoda nebyla nalezena“ při spuštění v Azure Cloud Services
Vytvořili jste sestavení pro .NET 4.6? Verze 4.6 není v rolích Azure Cloud Services podporována automaticky. Před spuštěním aplikace [nainstalujte pro každou roli verzi 4.6](../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Další postup
* [Konfigurace odesílání diagnostiky Azure do Application Insights](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)
* [Automatizace vytváření prostředků Application Insights](app-insights-powershell.md)
* [Automatizace diagnostiky Azure](app-insights-powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-overview.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md 
