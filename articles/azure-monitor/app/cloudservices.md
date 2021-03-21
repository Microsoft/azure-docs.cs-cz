---
title: Application Insights pro Azure Cloud Services | Microsoft Docs
description: Efektivní sledování webových rolí a rolí pracovních procesů s využitím Application Insights
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 09/05/2018
ms.openlocfilehash: 264d6d4b0b397a29b5dc1db4bb299297c1e30584
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419231"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights pro Azure Cloud Services
[Application Insights][start] můžou monitorovat [aplikace cloudových služeb Azure](https://azure.microsoft.com/services/cloud-services/) kvůli dostupnosti, výkonu, selhání a využití díky kombinování dat ze Application Insights sad SDK s [Azure Diagnosticsmi](../agents/diagnostics-extension-overview.md) daty z vašich cloudových služeb. Na základě zpětné vazby ohledně výkonu a efektivity vaší aplikace při běžném používání můžete informovaně rozhodovat o směrování návrhu v každé fázi vývoje.

![Řídicí panel přehled](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Předpoklady
Než začnete, budete potřebovat:

* Předplatné [Azure](https://azure.com) . Přihlaste se pomocí účet Microsoft pro Windows, Xbox Live nebo jiné cloudové služby Microsoftu. 
* Microsoft Azure Tools 2,9 nebo novější.
* Developer Analytics Tools 7,10 nebo novější.

## <a name="get-started-quickly"></a>Začínáme rychle
Nejrychlejším a nejjednodušším způsobem, jak monitorovat cloudovou službu s použitím Application Insights, je zvolit tuto možnost při publikování služby v Azure.

![Příklad stránky nastavení diagnostiky](./media/cloudservices/azure-cloud-application-insights.png)

Tato možnost zobrazí aplikaci za běhu a poskytne vám veškerou telemetrii, kterou potřebujete k monitorování požadavků, výjimek a závislostí ve webové roli. Monitoruje také čítače výkonu z vašich rolí pracovního procesu. Všechna trasování diagnostiky generovaná vaší aplikací se také odesílají do Application Insights.

Pokud potřebujete tuto možnost, jste hotovi. 

Vaše další kroky [zobrazují metriky z vaší aplikace](../essentials/metrics-charts.md)a [dotazují se na data s analýzou](../logs/log-query-overview.md). 

Chcete-li monitorovat výkon v prohlížeči, můžete také chtít nastavit [testy dostupnosti](./monitor-web-app-availability.md) a [přidat kód na webové stránky](./javascript.md).

Další části obsahují následující další možnosti:

* Odesílat data z různých komponent a sestavovat konfigurace pro oddělení prostředků.
* Přidání vlastní telemetrie ze své aplikace.

## <a name="sample-app-instrumented-with-application-insights"></a>Ukázková aplikace instrumentovaná pomocí Application Insights
V této [ukázkové aplikaci](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)se Application Insights přidá do cloudové služby se dvěma rolemi pracovního procesu hostovanými v Azure. 

V další části se dozvíte, jak přizpůsobit vlastní projekt cloudové služby stejným způsobem.

## <a name="plan-resources-and-resource-groups"></a>Plánování prostředků a skupin prostředků
Telemetrie z vaší aplikace se ukládají, analyzují a zobrazují v prostředku Azure typu Application Insights. 

Každý prostředek patří do skupiny prostředků. Skupiny prostředků se používají ke správě nákladů, k udělení přístupu členům týmu a k nasazení aktualizací v jedné koordinované transakci. Můžete například [napsat skript, který nasadí](../../azure-resource-manager/templates/deploy-powershell.md) cloudovou službu Azure a její Application Insights monitorování prostředků v jedné operaci.

### <a name="resources-for-components"></a>Prostředky pro komponenty

Doporučujeme [Přidat vlastnost dimenze do každé položky telemetrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) , která identifikuje její zdrojovou roli. V tomto přístupu grafy metrik, jako jsou například výjimky, obvykle zobrazují agregaci počtů z různých rolí, ale v případě potřeby můžete graf rozdělit podle identifikátoru role. Můžete také filtrovat hledání podle stejné dimenze. Tato alternativa usnadňuje zobrazení všeho ve stejnou chvíli, ale může také vést k nejasnostem mezi rolemi.

Telemetrická data prohlížeče jsou obvykle součástí stejného prostředku jako u příslušné serverové webové role.

Do jedné skupiny prostředků umístěte Application Insights prostředky pro různé součásti. Tento přístup usnadňuje jejich správu dohromady. 

### <a name="separate-development-test-and-production"></a>Samostatný vývoj, testování a provoz
Pokud pro další funkci vyvíjíte vlastní událostí a předchozí verze běží v živém provozu, je vhodné odesílat telemetrická data pro vývoj do samostatného prostředku Application Insights. V opačném případě může být obtížné najít svou telemetrii testů mezi všemi přenosy z živého webu.

Chcete-li se této situaci vyhnout, vytvořte samostatné prostředky pro každou konfiguraci sestavení nebo "razítko" (vývoj, testování, produkce atd.) systému. Prostředky pro každou konfiguraci sestavení umístěte do samostatné skupiny prostředků. 

Chcete-li odeslat telemetrii do příslušných prostředků, můžete nastavit sadu Application Insights SDK tak, aby v závislosti na konfiguraci sestavení vystavila jiný klíč instrumentace. 

Naučte se [dynamicky nastavovat klíč instrumentace](./separate-resources.md#dynamic-ikey) pro různé fáze. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Vytvoření prostředku Application Insights pro každou roli

Pokud jste se rozhodli vytvořit samostatný prostředek pro každou roli a případně pro každou konfiguraci sestavení samostatnou sadu, je nejjednodušší je vytvořit na portálu Application Insights. Pokud vytvoříte velké množství prostředků, můžete [proces automatizovat](./powershell.md).

1. V [Azure Portal][portal]vyberte možnost **nové**  >  **služby pro vývojáře**  >  **Application Insights**.  

    ![Application Insights podokno](./media/cloudservices/01-new.png)

1. V rozevíracím seznamu **Typ aplikace** vyberte možnost **Webová aplikace ASP.NET**.

Jednotlivé prostředky identifikuje klíč instrumentace. Tento klíč možná budete potřebovat později, pokud chcete ručně nakonfigurovat nebo ověřit konfiguraci sady SDK.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Nastavení diagnostiky Azure pro každou roli
Tuto možnost nastavte, pokud chcete aplikaci monitorovat pomocí Application Insights. U webových rolí Tato možnost poskytuje monitorování výkonu, výstrahy, diagnostiku a analýzu využití. U ostatních rolí můžete vyhledávat a monitorovat Azure Diagnostics, jako je například restart, čítače výkonu a volání System. Diagnostics. Trace. 

1. V aplikaci Visual Studio Průzkumník řešení v části **\<YourCloudService>**  >  **role** otevřete vlastnosti jednotlivých rolí.

1. V okně **Konfigurace** zaškrtněte políčko **Odeslat diagnostická data do Application Insights** a pak vyberte prostředek Application Insights, který jste vytvořili dříve.

Pokud jste se rozhodli použít samostatný prostředek Application Insights pro každou konfiguraci sestavení, vyberte nejprve konfiguraci.

![Konfigurace Application Insights](./media/cloudservices/configure-azure-diagnostics.png)

To má vliv na vložení klíčů instrumentace Application Insights do souborů s názvem *ServiceConfiguration. \* . cscfg*. Zde je [ukázkový kód](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Pokud chcete změnit úroveň diagnostických informací, které se odesílají do Application Insights, můžete to provést tak, že [přímo upravíte soubory *. cscfg*](../agents/diagnostics-extension-to-application-insights.md).

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>Instalace sady SDK v každém projektu
Pomocí této možnosti můžete přidat vlastní obchodní telemetrii k libovolné roli. Možnost poskytuje užší analýzu způsobu použití a provádění vaší aplikace.

V sadě Visual Studio nakonfigurujte sadu SDK Application Insights pro každý projekt cloudové aplikace.

1. Chcete-li nakonfigurovat **webové role**, klikněte pravým tlačítkem myši na projekt a vyberte možnost **Konfigurovat Application Insights** nebo **Přidat > Application Insights telemetrie**.

1. Postup při konfiguraci **rolí pracovního procesu**: 

    a. Klikněte pravým tlačítkem na projekt a pak vyberte **Spravovat balíčky NuGet**.

    b. Přidejte balíček [Application Insights pro servery Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

1. Konfigurace sady SDK pro posílání dat do prostředku Application Insights:

    a. Ve vhodné spouštěcí funkci nastavte klíč instrumentace z nastavení konfigurace v souboru *. cscfg* :
 
    ```csharp
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
        var telemetryClient = new TelemetryClient(configuration);
    ```
   
    b. Opakujte krok "Step a" pro každou roli ve vaší aplikaci. Podívejte se na příklady:
   
    * [Webová role](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Role pracovního procesu](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Pro webové stránky](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Nastavte soubor *ApplicationInsights.config* , který se má zkopírovat vždy do výstupního adresáře.

   Zpráva v souboru *. config* vás vyzve k zadání klíče instrumentace do tohoto umístění. Pro cloudové aplikace je ale lepší je nastavit ze souboru *. cscfg* . Tento přístup zajišťuje správnou identifikaci role na portálu.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Nastavení Monitorování stavu pro shromažďování úplných dotazů SQL (volitelné)

Tento krok je nutný pouze v případě, že chcete zachytit úplné dotazy SQL na .NET Framework. 

1. V `\*.csdef` úloze přidat [spouštěcí úlohu](../../cloud-services/cloud-services-startup-tasks.md) pro každou roli jako 

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
    
2. Stáhněte [InstallAgent.bat](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) a [InstallAgent.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1)je vložte do `AppInsightsAgent` složky v každém projektu role. Nezapomeňte je zkopírovat do výstupního adresáře prostřednictvím vlastností souboru sady Visual Studio nebo skriptů sestavení.

3. Na všech rolích pracovního procesu přidejte proměnné prostředí: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Spuštění a publikování aplikace

1. Spusťte aplikaci a přihlaste se k Azure. 

1. Otevřete Application Insights prostředky, které jste vytvořili.

   V [Průzkumníku metrik](../essentials/metrics-charts.md)se zobrazí jednotlivé datové [body a][diagnostic]agregovaná data se zobrazí.

1. Přidejte další telemetrii (podívejte se na další části) a pak publikujte aplikaci, abyste získali živou diagnostiku a používání zpětné vazby. 

Pokud žádná data neexistují, udělejte toto:

1. Chcete-li zobrazit jednotlivé události, otevřete dlaždici [hledání][diagnostic] .
1. V aplikaci otevřete různé stránky, aby se vygenerovala nějaká telemetrie.
1. Počkejte několik sekund a potom klikněte na tlačítko **aktualizovat**.  

Další informace naleznete v tématu [Poradce při potížích][qna].

## <a name="view-azure-diagnostics-events"></a>Zobrazit Azure Diagnostics události
Informace o [Azure Diagnostics](../agents/diagnostics-extension-overview.md) v Application Insights najdete v následujících umístěních:

* Čítače výkonu se zobrazují jako vlastní metriky. 
* Protokoly událostí systému Windows se zobrazují jako trasování a vlastní události.
* Protokoly aplikací, protokoly trasování událostí pro Windows a veškeré protokoly infrastruktury diagnostiky se zobrazují jako trasování.

Chcete-li zobrazit čítače výkonu a počty událostí, otevřete [Průzkumník metrik](../essentials/metrics-charts.md) a přidejte následující graf:

![Data Azure Diagnostics](./media/cloudservices/23-wad.png)

Pokud chcete hledat v různých protokolech trasování odesílaných pomocí Azure Diagnostics, použijte [vyhledávací](./diagnostic-search.md) dotaz nebo [dotaz Analytics](../logs/log-analytics-tutorial.md). Předpokládejme například, že máte neošetřenou výjimku, která způsobila selhání a recyklaci role. Tyto informace se zobrazují v kanálu Aplikace protokolu událostí systému Windows. Pomocí služby Search můžete zobrazit chybu protokolu událostí systému Windows a získat úplné trasování zásobníku pro výjimku. V takovém případě vám pomůže najít hlavní příčinu problému.

![Hledání Azure Diagnostics](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Další telemetrická data
V dalších částech najdete informace o tom, jak získat další telemetrii z různých aspektů aplikace.

## <a name="track-requests-from-worker-roles"></a>Sledování žádostí z rolí pracovního procesu
Ve webových rolí modul požadavků automaticky shromažďuje data týkající se požadavků HTTP. Příklady, jak můžete přepsat výchozí chování kolekce, najdete v [ukázce MVCWebRole](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Můžete zaznamenávat výkon volání rolí pracovních procesů, pokud je budete sledovat stejným způsobem jako požadavky HTTP. V Application Insights typ telemetrických dat Požadavek měří uvedenou jednotku práce serveru, kterou lze časovat a která může nezávisle být úspěšná nebo neúspěšná. I když jsou požadavky HTTP zachyceny automaticky sadou SDK, můžete vložit vlastní kód pro sledování požadavků na role pracovního procesu.

Podívejte se na dvě ukázkové role pracovních procesů, které jsou instrumentované na požadavky na sestavy: 
* [WorkerRoleA](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Výjimky
Informace o tom, jak shromažďovat neošetřené výjimky z různých typů webových aplikací, naleznete [v tématu výjimky monitorování v Application Insights](./asp-net-exceptions.md).

Ukázková webová role obsahuje kontrolery rozhraní MVC5 a Web API 2. Nezpracované výjimky z nich jsou zachyceny pomocí následujících obslužných rutin:

* [AiHandleErrorAttribute](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) nastavení pro řadiče MVC5 [, jak je znázorněno v tomto příkladu](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) nastavte pro řadiče webového rozhraní API 2 [, jak je znázorněno v tomto příkladu](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) . 

Pro role pracovního procesu můžete výjimky sledovat dvěma způsoby:

* Použijte TrackException (ex).
* Pokud jste přidali balíček NuGet naslouchacího procesu trasování Application Insights, můžete použít System. Diagnostics. Trace k protokolování výjimek, [jak je znázorněno v tomto příkladu](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Čítače výkonu
Ve výchozím nastavení se shromažďují následující čítače:

* \Process(?? APP_WIN32_PROC??) \% Čas procesoru
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Pro webové role se shromažďují i tyto čítače:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Můžete určit další vlastní nebo jiné čítače výkonu systému Windows úpravou *ApplicationInsights.config* [, jak je znázorněno v tomto příkladu](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Čítače výkonu](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Korelační telemetrie pro role pracovního procesu
Pro prostředí s bohatou diagnostikou si můžete zobrazit, co vedlo k žádosti o neúspěšnou nebo vysokou latenci. V případě webových rolí sada SDK automaticky nastaví korelaci mezi související telemetrie. 

Chcete-li dosáhnout tohoto zobrazení pro role pracovního procesu, můžete použít vlastní inicializátor telemetrie k nastavení běžného atributu kontextu Operation.Id pro všechny telemetrie. To vám umožní zobrazit na první pohled, zda došlo k potížím s latencí nebo chybou v důsledku závislosti nebo kódu. 

Jak na to:

* Nastavte ID korelace na CallContext [, jak je znázorněno v tomto příkladu](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). V tomto případě používáme ID žádosti jako ID korelace.
* Přidejte vlastní implementaci TelemetryInitializer, abyste mohli nastavit Operation.Id na ID korelace, které bylo dříve nastaveno. Příklad naleznete v tématu [ItemCorrelationTelemetryInitializer](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Přidejte inicializátor vlastní telemetrie. Můžete to udělat v souboru *ApplicationInsights.config* nebo v kódu [, jak je znázorněno v tomto příkladu](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetrická data klienta
Chcete-li získat telemetrii založenou na prohlížeči, jako je počet zobrazení stránky, doba načítání stránky nebo výjimky skriptu a psaní vlastní telemetrie ve skriptech stránky, přečtěte si téma [Přidání sady JavaScript SDK na webové stránky][client].

## <a name="availability-tests"></a>Testy dostupnosti
Chcete-li zajistit, aby vaše aplikace zůstala živá a reagovat, [nastavte webové testy][availability].

## <a name="display-everything-together"></a>Zobrazení všeho najednou
Pro celkový přehled systému můžete na jednom [řídicím panelu](./overview-dashboard.md)Zobrazit klíčové grafy monitorování. Můžete například připnout počty požadavků a selhání pro jednotlivé role. 

Pokud váš systém používá jiné služby Azure, například Stream Analytics, zahrňte také jejich sledovací grafy. 

Pokud máte mobilní klientskou aplikaci, použijte [App Center](../app/mobile-center-quickstart.md). V [Analytics](../logs/log-query-overview.md) můžete vytvářet dotazy pro zobrazení počtu událostí a můžete je připnout na řídicí panel.

## <a name="example"></a>Příklad
V [příkladu](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) se monitoruje služba s webovou rolí a dvěma rolemi pracovních procesů.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Výjimka "metoda nebyla nalezena" při spuštění v Azure Cloud Services
Vytvořili jste sestavení pro .NET 4.6? Rozhraní .NET 4,6 se v rolích Azure Cloud Services nepodporuje automaticky. Před spuštěním vaší aplikace [nainstalujte do každé role .net 4,6](../../cloud-services/cloud-services-dotnet-install-dotnet.md) .

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Další kroky
* [Konfigurace odesílání diagnostiky Azure do Application Insights](../agents/diagnostics-extension-to-application-insights.md)
* [Automatické vytváření Application Insightsch prostředků](./powershell.md)
* [Automatizace Azure Diagnostics](./powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ./api-custom-events-metrics.md
[availability]: ./monitor-web-app-availability.md
[azure]: ./app-insights-overview.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[netlogs]: ./asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md