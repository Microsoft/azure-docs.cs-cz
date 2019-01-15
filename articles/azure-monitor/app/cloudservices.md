---
title: Application Insights pro Azure cloud services | Dokumentace Microsoftu
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
ms.openlocfilehash: d27c0e9570959e01267d83a768ead45b48b7cea1
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267228"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights pro Azure cloud services
[Application Insights] [ start] můžete monitorovat [aplikací Azure cloud service](https://azure.microsoft.com/services/cloud-services/) pro dostupnost, výkon, chyby a využití díky kombinování dat ze sady SDK služby Application Insights s [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) data z vašich cloudových služeb. Na základě zpětné vazby ohledně výkonu a efektivity vaší aplikace při běžném používání můžete informovaně rozhodovat o směrování návrhu v každé fázi vývoje.

![Řídicího panelu s přehledem](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Požadavky
Než začnete, budete potřebovat:

* [Azure](https://azure.com) předplatného. Přihlaste se pomocí svého účtu Microsoft pro Windows, Xbox Live a dalším cloudovým službám Microsoftu. 
* Nástroje Microsoft Azure 2.9 nebo novější.
* Developer Analytics Tools 7.10 nebo novější.

## <a name="get-started-quickly"></a>Rychlý začátek
Nejrychlejším a nejjednodušším způsobem, jak monitorovat cloudovou službu s použitím Application Insights, je zvolit tuto možnost při publikování služby v Azure.

![Příklad stránky nastavení diagnostiky](./media/cloudservices/azure-cloud-application-insights.png)

Tato možnost využívá vaši aplikaci za běhu a poskytuje vám veškerá telemetrická data, které potřebujete monitorovat požadavky, výjimky a závislosti webové role. Také monitoruje čítače výkonu z rolí pracovních procesů. Žádné diagnostické trasování vygenerovaný vaší aplikací se také odesílají do Application Insights.

Pokud tato možnost je vše, co potřebujete, jste hotovi. 

Další kroky jsou [zobrazení metrik z aplikace](../../azure-monitor/app/metrics-explorer.md), [zadávání dotazů na data pomocí Analytics](../../azure-monitor/app/analytics.md)a případně i nastavení [řídicí panel](../../azure-monitor/app/app-insights-dashboards.md). 

K monitorování výkonu v prohlížeči, můžete také chtít nastavit [testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) a [přidání kódu do své webové stránky](../../azure-monitor/app/javascript.md).

Následující části popisují následující další možnosti:

* Odesílání dat z různých součástí a konfigurace k oddělení prostředků sestavení.
* Přidání vlastní telemetrie ze své aplikace.

## <a name="sample-app-instrumented-with-application-insights"></a>Ukázková aplikace používaná s Application Insights
V tomto [ukázkovou aplikaci](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), Application Insights se přidá do cloudové služby s dvěma rolemi pracovních procesů hostovanými v Azure. 

V další části se dozvíte, jak přizpůsobit stejně jako vlastní projekt cloudové služby.

## <a name="plan-resources-and-resource-groups"></a>Plánování prostředků a skupin prostředků
Telemetrie z vaší aplikace je uložené, analyzují a zobrazují v prostředku Azure typu Application Insights. 

Každý prostředek patří do skupiny prostředků. Skupiny prostředků slouží ke správě nákladů, udělit přístup ke členům týmu a k nasazování aktualizací v rámci jedné koordinované transakce. Například může [napsat skript pro nasazení](../../azure-resource-manager/resource-group-template-deploy.md) cloudové služby Azure a jeho Application Insights, monitorování prostředků v rámci jedné operace.

### <a name="resources-for-components"></a>Prostředky pro komponenty
Doporučujeme vytvořit samostatný prostředek pro každou komponentu aplikace. To znamená, že vytvoříte prostředek pro každou webovou roli a roli pracovního procesu. Jednotlivé komponenty můžete analyzovat samostatně, ale můžete vytvořit [řídicí panel](../../azure-monitor/app/app-insights-dashboards.md) , který umožňuje mít pohromadě klíčové grafy ze všech komponent tak, aby mohli porovnávat a monitorovat jejich společně v jednom zobrazení. 

Alternativním přístupem je odesílání telemetrických dat z více než jedné role do stejného prostředku, ale [přidáním vlastnosti dimenze pro každou položku telemetrie](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer) , která je určena její zdrojová role. V takovém případě grafy metrik, jako jsou například výjimky, normálně zobrazuje agregace počtů z různých rolí, ale můžete graf segmentovat podle identifikátoru role podle potřeby. Můžete také filtrovat hledání podle stejné dimenze. Tato alternativa usnadňuje trochu Zobrazit vše, co ve stejnou dobu, ale může také způsobit nejasnostem mezi rolemi.

Telemetrická data prohlížeče jsou obvykle součástí stejného prostředku jako u příslušné serverové webové role.

Vložte prostředky Application Insights pro různé součásti v jedné skupině prostředků. Tento přístup umožňuje snadno spravovat je společně. 

### <a name="separate-development-test-and-production"></a>Samostatný vývoj, testování a provoz
Pokud pro další funkci vyvíjíte vlastní událostí a předchozí verze běží v živém provozu, je vhodné odesílat telemetrická data pro vývoj do samostatného prostředku Application Insights. V opačném případě může být obtížné najít váš testovací telemetrická data veškerý provoz z živého webu.

Abyste předešli této situaci, vytvořte samostatné prostředky pro každou konfiguraci sestavení nebo "razítko" (vývojové, testovací, výrobní a tak dále) vašeho systému. Prostředky pro každou konfiguraci sestavení umístěte do samostatné skupiny prostředků. 

Odesílání telemetrických dat do příslušných prostředků, můžete nastavit sadu SDK Application Insights tak, aby použila jiný Instrumentační klíč, v závislosti na konfiguraci sestavení. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Vytvoření prostředku Application Insights pro každou roli

Pokud jste se rozhodli vytvořit samostatný prostředek pro každou roli, a možná samostatnou sadu pro každou konfiguraci sestavení, je nejjednodušší vytvořit je všechny na portálu služby Application Insights. Pokud vytvoříte prostředků hodně, můžete [automatizaci procesu](../../azure-monitor/app/powershell.md).

1. V [webu Azure portal][portal]vyberte **nový** > **vývojářské služby**  >   **Application Insights**.  

    ![Podokno Application Insights](./media/cloudservices/01-new.png)

1. V **typ aplikace** rozevíracího seznamu vyberte **webová aplikace ASP.NET**.  
    Každý prostředek je identifikován instrumentačním klíčem. Tento klíč může potřebovat později, pokud chcete ručně nakonfigurovat nebo ověřit konfiguraci sady SDK.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Nastavení diagnostiky Azure pro každou roli
Tuto možnost nastavte, pokud chcete aplikaci monitorovat pomocí Application Insights. V případě webových rolí tato možnost poskytuje performance monitoring pro aplikace, výstrahy, diagnostiku a analýzy využití. Pro jiné role můžete vyhledat a monitorovat diagnostiku Azure jako je restartování, čítače výkonu a volání System.Diagnostics.Trace. 

1. V Průzkumníku řešení Visual Studio v části  **\<Vaše_cloudová_služba >** > **role**, otevřete vlastnosti jednotlivých rolí.

1. V **konfigurace**, vyberte **posílat diagnostická data do Application Insights** zaškrtněte políčko a pak vyberte prostředek Application Insights, který jste vytvořili dříve.

Pokud jste se rozhodli použít samostatný prostředek Application Insights pro každou konfiguraci sestavení, vyberte nejprve konfiguraci.

![Nakonfigurovat Application Insights](./media/cloudservices/configure-azure-diagnostics.png)

To má za následek vložení instrumentačních klíčů Application Insights do souborů s názvem *ServiceConfiguration.\*. cscfg*. Tady je [ukázkový kód](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Pokud chcete měnit úroveň diagnostických informací odesílaných do Application Insights, můžete tak učinit [úpravou *.cscfg* soubory přímo](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

## <a name="sdk"></a>Instalace sady SDK v každém projektu
Pomocí této možnosti můžete přidat vlastní obchodní telemetrická data k žádné roli. Možnost poskytuje podrobnější analýze toho, jak vaše aplikace se používá a provádí.

V sadě Visual Studio nakonfigurujte sadu SDK Application Insights pro každý projekt cloudové aplikace.

1. Ke konfiguraci **webové role**, klikněte pravým tlačítkem na projekt a pak vyberte **konfigurovat Application Insights** nebo **Přidat > telemetrii Application Insights**.

1. Ke konfiguraci **rolí pracovního procesu**: 

    a. Klikněte pravým tlačítkem na projekt a pak vyberte **spravovat balíčky NuGet**.

    b. Přidejte balíček [Application Insights pro servery Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Vyhledání Application Insights](./media/cloudservices/04-ai-nuget.png)

1. Konfigurace sady SDK k odesílání dat do prostředku Application Insights:

    a. Ve vhodné spouštěcí funkci nastavte Instrumentační klíč podle nastavení konfigurace v *.cscfg* souboru:
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Opakujte "krok" pro každou roli v aplikaci. Podívejte se na příklady:
   
    * [Webová role](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Role pracovního procesu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Pro webové stránky](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Nastavte *soubor ApplicationInsights.config* souboru se vždy kopíroval do výstupního adresáře.  
    Zpráva v *.config* souboru vás požádá, abyste umístili Instrumentační klíč existuje. Pro cloudové aplikace, je však lepší provést nastavení ze *.cscfg* souboru. Tento přístup zajišťuje, že role je správně identifikovat na portálu.

#### <a name="run-and-publish-the-app"></a>Spuštění a publikování aplikace

1. Spusťte aplikaci a přihlaste se k Azure. 

1. Otevřete prostředky Application Insights, které jste vytvořili.  
    Jednotlivé datové body jsou zobrazeny v [hledání](../../azure-monitor/app/diagnostic-search.md), a agregovaná data se zobrazí v [Průzkumník metrik](../../azure-monitor/app/metrics-explorer.md). 

1. Přidání další telemetrie (viz další části) a pak publikujte svou aplikaci můžete získat zpětnou za využití a Diagnostika. 

Pokud nejsou žádná data, postupujte takto:
1. Chcete-li zobrazit jednotlivé události, otevřete [hledání] [ diagnostic] dlaždici.
1. V aplikaci otevřete různé stránky tak, aby vygenerování nějaké telemetrie.
1. Počkejte několik sekund a potom klikněte na tlačítko **aktualizovat**.  
    Další informace najdete v tématu [Poradce při potížích s][qna].

## <a name="view-azure-diagnostics-events"></a>Zobrazení událostí diagnostiky Azure
Můžete najít [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) informace ve službě Application Insights v následujících umístěních:

* Čítače výkonu se zobrazují jako vlastní metriky. 
* Protokoly událostí systému Windows se zobrazují jako trasování a vlastní události.
* Protokoly aplikací, protokoly trasování událostí pro Windows a veškeré protokoly infrastruktury diagnostiky se zobrazují jako trasování.

Chcete-li zobrazit čítače výkonu a počty událostí, otevřete [Průzkumníka metrik](../../azure-monitor/app/metrics-explorer.md) a přidejte následující diagram:

![Diagnostická data Azure](./media/cloudservices/23-wad.png)

Pokud chcete vyhledávat v různých protokolech trasování odeslaných diagnostikou Azure, použijte [hledání](../../azure-monitor/app/diagnostic-search.md) nebo [dotazu Analytics](../../azure-monitor/log-query/get-started-portal.md). Předpokládejme například, že se vyskytla neošetřená výjimka, která způsobila zhroucení a recyklaci role. Tyto informace se zobrazují v kanálu Aplikace protokolu událostí systému Windows. Hledání můžete použít k zobrazení chyb protokolu událostí Windows a získat úplné trasování zásobníku pro výjimku. To vám pomůže najít hlavní příčinu problému.

![Hledání v diagnostice Azure](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Další telemetrická data
Následující části popisují postup získali další telemetrické údaje z různých aspektů aplikace.

## <a name="track-requests-from-worker-roles"></a>Sledování požadavků z rolí pracovních procesů
Ve webových rolí modul požadavků automaticky shromažďuje data týkající se požadavků HTTP. Příklady Potlačení výchozího chování při shromažďování, najdete v článku [ukázkový MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Můžete zaznamenávat výkon volání rolí pracovních procesů, pokud je budete sledovat stejným způsobem jako požadavky HTTP. V Application Insights typ telemetrických dat Požadavek měří uvedenou jednotku práce serveru, kterou lze časovat a která může nezávisle být úspěšná nebo neúspěšná. I když požadavky HTTP automaticky zachycuje sada SDK, můžete vložit vlastní kód pro sledování požadavků na role pracovních procesů.

Zobrazit dvěma rolemi pracovních procesů ukázkové požadavky: 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Výjimky
Informace o tom, jak shromažďovat neošetřené výjimky z různých typů webových aplikací najdete v tématu [monitorování výjimek v Application Insights](../../azure-monitor/app/asp-net-exceptions.md).

Ukázková webová role obsahuje kontrolery rozhraní MVC5 a Web API 2. Nezpracované výjimky z nich jsou zachyceny pomocí následujících obslužných rutin:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) nastavit pro kontrolery rozhraní MVC5 [jak je znázorněno v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) nastavenou službu Web API 2 řadiče [jak je znázorněno v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

U rolí pracovního procesu můžete sledovat výjimky dvěma způsoby:

* Použijte TrackException(ex).
* Pokud jste přidali balíček NuGet naslouchací proces trasování Application Insights, můžete k protokolování výjimek použít System.Diagnostics.Trace [jak je znázorněno v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

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

Můžete zadat další vlastní nebo jiné čítače výkonu Windows tak, že upravíte *soubor ApplicationInsights.config* [jak je znázorněno v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Čítače výkonu](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Korelační telemetrická data pro role pracovních procesů
Pro prostředí bohatou diagnostikou můžete zobrazit, co vedlo k požadavku se nezdařilo nebo vysokou latencí. V případě webových rolí sada SDK automaticky zjišťuje korelaci mezi souvisejícími telemetrickými daty. 

K dosažení tohoto zobrazení, rolí pracovních procesů, můžete použít inicializátor vlastní telemetrie nastavit společný atribut kontextu Operation.Id pro veškerá telemetrická data. To umožňuje zobrazit na první pohled, jestli problém latence nebo selhání bylo způsobené některé závislosti nebo kódu. 

Zde je uveden postup:

* Nastavte ID korelace v CallContext [jak je znázorněno v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). V tomto případě používáme ID žádosti jako ID korelace.
* Přidejte vlastní implementaci TelemetryInitializer a nastavte Operation.Id na ID korelace, která byla dříve nastavena. Příklad najdete v tématu [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Přidejte inicializátor vlastní telemetrie. Se dá dělat, *soubor ApplicationInsights.config* souboru nebo v kódu [jak je znázorněno v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetrická data klienta
Získejte telemetrické informace založené na prohlížeči, jako jsou počty zobrazení stránek, časy načítání stránek nebo výjimky skriptů a zapisovat ve svých skriptech stránek vlastní telemetrická data, najdete v části [přidejte do své webové stránky JavaScript SDK][client].

## <a name="availability-tests"></a>Testy dostupnosti
Abyste měli jistotu, vaše aplikace zůstává aktivní a reagující, [nastavit webové testy][availability].

## <a name="display-everything-together"></a>Zobrazení všeho najednou
Získat celkový přehled o systému, můžete zobrazit klíčové grafy monitorování společně v jednom [řídicí panel](../../azure-monitor/app/app-insights-dashboards.md). Můžete například připnout počty požadavků a selhání pro jednotlivé role. 

Pokud váš systém využívá jiné služby Azure zahrnují například Stream Analytics, jejich monitorovacích grafů. 

Pokud máte mobilní klientskou aplikaci, použijte [App Center](../../azure-monitor/learn/mobile-center-quickstart.md). V [Analytics](../../azure-monitor/app/analytics.md) můžete vytvářet dotazy pro zobrazení počtu událostí a můžete je připnout na řídicí panel.

## <a name="example"></a>Příklad:
V [příkladu](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) se monitoruje služba s webovou rolí a dvěma rolemi pracovních procesů.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Výjimka "metoda nebyla nalezena" u spuštěných v Azure cloud services
Vytvořili jste sestavení pro .NET 4.6? .NET 4.6 není v rolích Azure cloud services podporována automaticky. [Nainstalovat .NET 4.6 pro každou roli](../../cloud-services/cloud-services-dotnet-install-dotnet.md) před spuštěním vaší aplikace.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Další postup
* [Konfigurace odesílání diagnostiky Azure do Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Automaticky vytvořit prostředky Application Insights](../../azure-monitor/app/powershell.md)
* [Automatizace diagnostiky Azure](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
