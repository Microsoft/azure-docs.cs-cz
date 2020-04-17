---
title: Přehledy aplikací pro cloudové služby Azure | Dokumenty společnosti Microsoft
description: Efektivní sledování webových rolí a rolí pracovních procesů s využitím Application Insights
ms.topic: conceptual
ms.date: 09/05/2018
ms.openlocfilehash: 17813d17a1c40caac5587e37e279be6376992b90
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537589"
---
# <a name="application-insights-for-azure-cloud-services"></a>Přehledy aplikací pro cloudové služby Azure
[Application Insights můžou][start] sledovat [dostupnost,](https://azure.microsoft.com/services/cloud-services/) výkon, selhání a využití aplikací Azure díky kombinaci dat z sad SDK application insights s [daty Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) z vašich cloudových služeb. Na základě zpětné vazby ohledně výkonu a efektivity vaší aplikace při běžném používání můžete informovaně rozhodovat o směrování návrhu v každé fázi vývoje.

![Přehledový řídicí panel](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Požadavky
Než začnete, potřebujete:

* Předplatné [Azure.](https://azure.com) Přihlaste se pomocí svého účtu Microsoft pro Windows, Xbox Live nebo jiné cloudové služby Microsoftu. 
* Nástroje Microsoft Azure 2.9 nebo novější.
* Nástroje pro analýzu vývojářů 7.10 nebo novější.

## <a name="get-started-quickly"></a>Rychlé zahájení
Nejrychlejším a nejjednodušším způsobem, jak monitorovat cloudovou službu s použitím Application Insights, je zvolit tuto možnost při publikování služby v Azure.

![Stránka Příklad nastavení diagnostiky](./media/cloudservices/azure-cloud-application-insights.png)

Tato možnost instrumentuje vaši aplikaci za běhu a poskytuje vám veškerou telemetricku, kterou potřebujete ke sledování požadavků, výjimek a závislostí ve vaší webové roli. Také monitoruje čítače výkonu z rolí pracovního procesu. Všechny diagnostické trasování generované vaší aplikací se také odešlou do Application Insights.

Pokud je tato možnost vše, co potřebujete, máte hotovo. 

Dalším krokem je [zobrazení metrik z aplikace](../../azure-monitor/platform/metrics-charts.md)a [dotazování na data pomocí služby Analytics](../../azure-monitor/app/analytics.md). 

Chcete-li sledovat výkon v prohlížeči, můžete také nastavit [testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) a [přidat kód na webové stránky](../../azure-monitor/app/javascript.md).

V následujících částech se popisují následující další možnosti:

* Odesílejte data z různých součástí a vytvářejte konfigurace do samostatných prostředků.
* Přidání vlastní telemetrie ze své aplikace.

## <a name="sample-app-instrumented-with-application-insights"></a>Ukázková aplikace instrumentovaná pomocí přehledů aplikací
V této [ukázkové aplikaci](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)application insights se přidá do cloudové služby se dvěma rolemi pracovních rolí hostovanými v Azure. 

V další části se dozvíte, jak přizpůsobit svůj vlastní projekt cloudových služeb stejným způsobem.

## <a name="plan-resources-and-resource-groups"></a>Plánování prostředků a skupin prostředků
Telemetrie z vaší aplikace se ukládá, analyzuje a zobrazuje v prostředku Azure typu Application Insights. 

Každý prostředek patří do skupiny prostředků. Skupiny prostředků se používají ke správě nákladů, udělení přístupu členům týmu a nasazení aktualizací v jedné koordinované transakci. Můžete například [napsat skript pro nasazení](../../azure-resource-manager/templates/deploy-powershell.md) cloudové služby Azure a jejích prostředků monitorování Application Insights v jedné operaci.

### <a name="resources-for-components"></a>Prostředky pro komponenty
Doporučujeme vytvořit samostatný prostředek pro každou součást aplikace. To znamená, že vytvoříte prostředek pro každou webovou roli a roli pracovního procesu. Každou komponentu můžete analyzovat samostatně, ale vytvoříte [řídicí panel,](../../azure-monitor/app/overview-dashboard.md) který sdružuje klíčové grafy ze všech součástí, takže je můžete porovnat a sledovat společně v jednom zobrazení. 

Alternativní přístup je odeslat telemetrická data z více než jedné role do stejného prostředku, ale [přidat vlastnost dimenze ke každé položce telemetrie,](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) která identifikuje jeho zdrojovou roli. V tomto přístupu metrické grafy, jako jsou výjimky, obvykle zobrazují agregaci počty z různých rolí, ale můžete segmentovat graf podle identifikátoru role, podle potřeby. Vyhledávání můžete také filtrovat podle stejné dimenze. Tato alternativa usnadňuje zobrazení vše ve stejnou dobu, ale může také vést k určité zmatek mezi rolemi.

Telemetrická data prohlížeče jsou obvykle součástí stejného prostředku jako u příslušné serverové webové role.

Vložte prostředky Application Insights pro různé součásti do jedné skupiny prostředků. Tento přístup usnadňuje jejich společné správě. 

### <a name="separate-development-test-and-production"></a>Samostatný vývoj, testování a provoz
Pokud pro další funkci vyvíjíte vlastní událostí a předchozí verze běží v živém provozu, je vhodné odesílat telemetrická data pro vývoj do samostatného prostředku Application Insights. V opačném případě může být obtížné najít test telemetrie mezi všechny provozy z živého webu.

Chcete-li se této situaci vyhnout, vytvořte samostatné prostředky pro každou konfiguraci sestavení nebo "razítko" (vývoj, testování, výroba a tak dále) systému. Prostředky pro každou konfiguraci sestavení umístěte do samostatné skupiny prostředků. 

Chcete-li odeslat telemetrická data do příslušných prostředků, můžete nastavit sadu Application Insights SDK tak, aby v závislosti na konfiguraci sestavení vyzvedla jiný klíč instrumentace. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Vytvoření prostředku Application Insights pro každou roli

Pokud jste se rozhodli vytvořit samostatný prostředek pro každou roli a možná samostatnou sadu pro každou konfiguraci sestavení, je nejjednodušší je vytvořit všechny na portálu Application Insights. Pokud vytváříte prostředky hodně, můžete [automatizovat proces](../../azure-monitor/app/powershell.md).

1. Na [webu Azure Portal][portal]vyberte **New** > **Developer Services** > **Application Insights**.  

    ![Podokno Přehledy aplikací](./media/cloudservices/01-new.png)

1. V rozevíracím seznamu **Typ aplikace** vyberte **ASP.NET webovou aplikaci**.

Každý prostředek je identifikován klíčem instrumentace. Tento klíč budete možná později potřebovat, pokud chcete ručně nakonfigurovat nebo ověřit konfiguraci sady SDK.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Nastavení diagnostiky Azure pro každou roli
Tuto možnost nastavte, pokud chcete aplikaci monitorovat pomocí Application Insights. Pro webové role tato možnost poskytuje sledování výkonu, výstrahy, diagnostiku a analýzu využití. Pro ostatní role můžete vyhledávat a monitorovat diagnostiku Azure, jako je restartování, čítače výkonu a volání System.Diagnostics.Trace. 

1. V Průzkumníku řešení Visual Studia v části  >  ** \<YourCloudService>** **role**otevřete vlastnosti každé role.

1. V **části Konfigurace**zaškrtněte políčko **Odeslat diagnostická data do application insights** a pak vyberte prostředek Application Insights, který jste vytvořili dříve.

Pokud jste se rozhodli použít samostatný prostředek Application Insights pro každou konfiguraci sestavení, vyberte nejprve konfiguraci.

![Konfigurace přehledů aplikací](./media/cloudservices/configure-azure-diagnostics.png)

To má za následek vložení klíčů instrumentace Application Insights do souborů s názvem *ServiceConfiguration.\*. cscfg*. Zde je [ukázkový kód](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Pokud chcete změnit úroveň diagnostických informací odeslaných do Application Insights, můžete tak učinit [přímo upravili soubory *CSCFG* ](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>Instalace sady SDK v každém projektu
Pomocí této možnosti můžete přidat vlastní obchodní telemetrie do libovolné role. Tato možnost poskytuje podrobnější analýzu toho, jak se vaše aplikace používá a provádí.

V sadě Visual Studio nakonfigurujte sadu SDK Application Insights pro každý projekt cloudové aplikace.

1. Chcete-li konfigurovat **webové role**, klepněte pravým tlačítkem myši na projekt a potom vyberte **konfigurovat přehledy aplikací** nebo přidat > **telemetrie Application Insights**.

1. Konfigurace **rolí pracovního procesu**: 

    a. Klikněte pravým tlačítkem myši na projekt a potom vyberte **příkaz Spravovat balíčky NuGet**.

    b. Přidejte balíček [Application Insights pro servery Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Vyhledejte „Application Insights“](./media/cloudservices/04-ai-nuget.png)

1. Postup konfigurace sady SDK pro odesílání dat do prostředku Application Insights:

    a. Ve vhodné funkci spuštění nastavte klíč instrumentace z nastavení konfigurace v souboru *.cscfg:*
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Opakujte "krok a" pro každou roli ve vaší aplikaci. Podívejte se na příklady:
   
    * [Webová role](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Role pracovního procesu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Pro webové stránky](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Nastavte soubor *ApplicationInsights.config* tak, aby byl vždy zkopírován do výstupního adresáře.

   Zpráva v souboru *.config* vás vyzve k umístění klíče instrumentace. Pro cloudové aplikace je však lepší nastavit ze souboru *.cscfg.* Tento přístup zajišťuje, že role je správně identifikován na portálu.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Nastavit sledování stavu pro shromažďování úplných dotazů SQL (volitelné)

Tento krok je potřeba pouze v případě, že chcete zachytit úplné dotazy SQL v rozhraní .NET Framework. 

1. V `\*.csdef` souboru Přidat [úlohu spuštění](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks) pro každou roli podobnou 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. Stáhněte si [InstallAgent.bat](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) a [InstallAgent.ps1](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1), vložte je do `AppInsightsAgent` složky na každém projektu role. Nezapomeňte je zkopírovat do výstupního adresáře prostřednictvím vlastností souboru sady Visual Studio nebo vytvářet skripty.

3. Ve všech rolích pracovního procesu přidejte proměnné prostředí: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Spuštění a publikování aplikace

1. Spusťte aplikaci a přihlaste se do Azure. 

1. Otevřete prostředky Application Insights, které jste vytvořili.

   Jednotlivé datové body jsou zobrazeny ve [vyhledávání][diagnostic]a agregovaná data jsou zobrazena v [Průzkumníku metrik](../../azure-monitor/platform/metrics-charts.md).

1. Přidejte další telemetrii (viz další části) a pak publikujte aplikaci, abyste získali živou diagnostiku a zpětnou vazbu od využití. 

Pokud nejsou k dispozici žádná data, postupujte takto:

1. Chcete-li zobrazit jednotlivé události, otevřete dlaždici [Hledat.][diagnostic]
1. V aplikaci otevřete různé stránky tak, aby vygenerovaly některé telemetrie.
1. Počkejte několik sekund a klepněte na tlačítko **Aktualizovat**.  

Další informace naleznete v tématu [Poradce při potížích][qna].

## <a name="view-azure-diagnostics-events"></a>Zobrazení událostí diagnostiky Azure
Informace o [diagnostice Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) najdete v Přehledech aplikací v následujících umístěních:

* Čítače výkonu se zobrazují jako vlastní metriky. 
* Protokoly událostí systému Windows se zobrazují jako trasování a vlastní události.
* Protokoly aplikací, protokoly trasování událostí pro Windows a veškeré protokoly infrastruktury diagnostiky se zobrazují jako trasování.

Chcete-li zobrazit čítače výkonu a počty událostí, otevřete [Průzkumník metrik](../../azure-monitor/platform/metrics-charts.md) a přidejte následující graf:

![Data diagnostiky Azure](./media/cloudservices/23-wad.png)

Chcete-li prohledávat různé protokoly trasování odeslané službou Azure Diagnostics, použijte [vyhledávací dotaz](../../azure-monitor/app/diagnostic-search.md) nebo [dotaz Analytics](../../azure-monitor/log-query/get-started-portal.md). Předpokládejme například, že máte neošetřenou výjimku, která způsobila selhání a recyklaci role. Tyto informace se zobrazují v kanálu Aplikace protokolu událostí systému Windows. Pomocí funkce Hledat můžete zobrazit chybu protokolu událostí systému Windows a získat trasování celého zásobníku pro výjimku. To vám pomůže najít hlavní příčinu problému.

![Azure Diagnostika hledání](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Další telemetrická data
V dalších částech se popisuje, jak získat další telemetrická data z různých aspektů vaší aplikace.

## <a name="track-requests-from-worker-roles"></a>Sledování požadavků z rolí pracovního procesu
Ve webových rolí modul požadavků automaticky shromažďuje data týkající se požadavků HTTP. Příklady, jak můžete přepsat výchozí chování kolekce, naleznete v [ukázkové mvcwebrole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Můžete zaznamenávat výkon volání rolí pracovních procesů, pokud je budete sledovat stejným způsobem jako požadavky HTTP. V Application Insights typ telemetrických dat Požadavek měří uvedenou jednotku práce serveru, kterou lze časovat a která může nezávisle být úspěšná nebo neúspěšná. Přestože požadavky HTTP jsou automaticky zachyceny sadou SDK, můžete vložit vlastní kód pro sledování požadavků na role pracovního procesu.

Podívejte se na dvě role dílčího pracovního procesu instrumentované pro hlášení požadavků: 
* [WorkerRolea](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Výjimky
Informace o tom, jak shromažďovat neošetřené výjimky z různých typů webových aplikací, naleznete [v tématu Sledování výjimek v application insights](../../azure-monitor/app/asp-net-exceptions.md).

Ukázková webová role obsahuje kontrolery rozhraní MVC5 a Web API 2. Nezpracované výjimky z nich jsou zachyceny pomocí následujících obslužných rutin:

* [Atribut AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) nastavenpro řadiče MVC5, [jak je znázorněno v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) nastavený pro řadiče webového rozhraní API [2, jak je znázorněno v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

U rolí pracovního procesu můžete sledovat výjimky dvěma způsoby:

* Použijte TrackException(ex).
* Pokud jste přidali balíček nugetového procesu trasování Application Insights, můžete použít System.Diagnostics.Trace k protokolování [výjimek, jak je znázorněno v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Čítače výkonu
Ve výchozím nastavení se shromažďují následující čítače:

* \Process(?? APP_WIN32_PROC?) \% Čas procesoru
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Pro webové role se shromažďují i tyto čítače:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Další vlastní nebo jiné čítače výkonu systému Windows můžete zadat úpravou *souboru ApplicationInsights.config,* [jak je znázorněno v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Čítače výkonu](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Korelační telemetrie pro role pracovního procesu
Pro bohaté diagnostické prostředí můžete zobrazit, co vedlo k neúspěšnému nebo požadavku s vysokou latencí. S webovými rolemi sada SDK automaticky nastaví korelaci mezi související telemetrií. 

Chcete-li dosáhnout tohoto zobrazení pro role pracovního procesu, můžete použít vlastní telemetrický inicializátor k nastavení společného Operation.Id kontextový atribut pro všechny telemetrie. To vám umožní zobrazit na první pohled, zda byl problém s latencí nebo selháním způsoben závislostí nebo vaším kódem. 

Zde je uveden postup:

* Nastavte correlationId do [CallContext, jak je znázorněno v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). V tomto případě používáme ID požadavku jako correlationId.
* Přidejte vlastní implementaci TelemetryInitializer, chcete-li nastavit Operation.Id na correlationId, která byla nastavena dříve. Příklad naleznete v tématu [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Přidejte inicializátor vlastní telemetrie. Můžete tak učinit v souboru *ApplicationInsights.config* nebo v [kódu, jak je znázorněno v tomto příkladu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetrická data klienta
Informace o získání telemetrie založené na prohlížeči, jako jsou počty zobrazení stránek, časy načítání stránek nebo výjimky skriptů, a psaní vlastní telemetrie do skriptů stránky, přečtěte si část Přidání sady [JavaScript SDK na webové stránky][client].

## <a name="availability-tests"></a>Testy dostupnosti
Chcete-li zajistit, aby aplikace zůstala aktivní a responzivní, [nastavte webové testy][availability].

## <a name="display-everything-together"></a>Zobrazení všeho najednou
Celkový obrázek systému získáte společně s klíčovými grafy monitorování na jednom [řídicím panelu](../../azure-monitor/app/overview-dashboard.md). Můžete například připnout počty požadavků a selhání pro jednotlivé role. 

Pokud váš systém používá jiné služby Azure, jako je Stream Analytics, zahrnout jejich monitorování grafy také. 

Pokud máte mobilní klientskou aplikaci, použijte [App Center](../../azure-monitor/learn/mobile-center-quickstart.md). V [Analytics](../../azure-monitor/app/analytics.md) můžete vytvářet dotazy pro zobrazení počtu událostí a můžete je připnout na řídicí panel.

## <a name="example"></a>Příklad
V [příkladu](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) se monitoruje služba s webovou rolí a dvěma rolemi pracovních procesů.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Výjimka "metoda nebyla nalezena" při spuštění v cloudových službách Azure
Vytvořili jste sestavení pro .NET 4.6? .NET 4.6 není automaticky podporována v rolích cloudových služeb Azure. Před spuštěním aplikace [nainstalujte do každé role .NET 4.6.](../../cloud-services/cloud-services-dotnet-install-dotnet.md)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Další kroky
* [Konfigurace odesílání diagnostiky Azure do Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Automatické vytváření prostředků Application Insights](../../azure-monitor/app/powershell.md)
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
