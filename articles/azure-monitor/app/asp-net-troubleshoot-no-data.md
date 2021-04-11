---
title: Řešení potíží s chybějícími daty v nástroji Application Insights pro .NET
description: Nezobrazuje se data v Azure Application Insights? Zkuste to prosím tady.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/21/2020
ms.openlocfilehash: fbf53f6d4a928215d25874f4e405147c73cbf81f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056568"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Řešení potíží bez Application Insights dat pro .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>Chybí některé moje telemetrie.
*V Application Insights se zobrazuje jenom zlomek událostí, které aplikace generuje.*

* Pokud se stále zobrazuje stejný zlomek, je pravděpodobně v důsledku adaptivního [vzorkování](./sampling.md). Potvrďte to tak, že otevřete hledání (z okna Přehled) a podíváte se na instanci žádosti nebo jinou událost. V dolní části oddílu Vlastnosti klikněte na... získat podrobnosti o úplných vlastnostech. Pokud se počet žádostí > 1, bude vzorkování v provozu.
* V opačném případě je možné, že jste u cenového plánu dosáhli [limitu](./pricing.md#limits-summary) přenosové rychlosti. Tato omezení se aplikují za minutu.

*Dochází k náhodnému výpadku dat.*

* Podívejte se, jestli u [kanálu telemetrie](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost) nedochází ke ztrátě dat.

* Vyhledat všechny známé problémy v [úložišti GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) kanálu telemetrie

*Došlo ke ztrátě dat v konzolové aplikaci nebo ve webové aplikaci, když se aplikace chystá zastavit.*

* Kanál SDK udržuje telemetrii ve vyrovnávací paměti a odesílá je v dávkách. Pokud se aplikace vypíná, bude pravděpodobně nutné explicitně zavolat metodu [flush ()](api-custom-events-metrics.md#flushing-data). Chování `Flush()` závisí na samotném použitém [kanálu](telemetry-channels.md#built-in-telemetry-channels) .

## <a name="no-data-from-my-server"></a>Žádná data z mého serveru
*Mám aplikaci nainstalovanou na mém webovém serveru a teď mi nevidím žádnou telemetrii. V mém vývojovém počítači se pracovalo v pořádku.*

* Pravděpodobně se jedná o problém s bránou firewall. [Nastavte výjimky brány firewall pro Application Insights pro odesílání dat](./ip-addresses.md).
* V serveru IIS chybí některé předpoklady: rozšiřitelnost rozhraní .NET 4,5 a ASP.NET 4,5.

*Nainstaloval (a) jsem [monitorování stavu](./monitor-performance-live-website-now.md) na mém webovém serveru, aby se sledovaly stávající aplikace. Nezobrazují se žádné výsledky.*

* Další informace najdete v tématu [řešení potíží s monitorování stavu](./monitor-performance-live-website-now.md#troubleshoot).

> [!IMPORTANT]
> Nové oblasti Azure **vyžadují** použití připojovacích řetězců místo klíčů instrumentace. [Připojovací řetězec](./sdk-connection-string.md?tabs=net) identifikuje prostředek, ke kterému chcete přidružit data telemetrie. Umožňuje také upravit koncové body, které prostředek použije jako cíl pro vaši telemetrii. Budete muset zkopírovat připojovací řetězec a přidat ho do kódu aplikace nebo do proměnné prostředí.


## <a name="filenotfoundexception-could-not-load-file-or-assembly-microsoftaspnet-telemetrycorrelation"></a>FileNotFoundException: nepovedlo se načíst soubor nebo sestavení Microsoft. AspNet TelemetryCorrelation.

Další informace o této chybě najdete v článku [problém GitHubu 1610] ( https://github.com/microsoft/ApplicationInsights-dotnet/issues/1610) .

Při upgradu ze sad SDK starších než (2,4) je nutné zajistit, aby byly v a provedeny následující změny `web.config` `ApplicationInsights.config` :

1. Dva moduly HTTP namísto jednoho. V `web.config` byste měli mít dva moduly HTTP. Pořadí je důležité v některých scénářích:

    ``` xml
    <system.webServer>
      <modules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="integratedMode,managedHandler" />
          <add name="ApplicationInsightsHttpModule" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
      </modules>
    </system.webServer>
    ```

2. `ApplicationInsights.config`Kromě byste `RequestTrackingTelemetryModule` měli mít následující modul telemetrie:

    ``` xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web"/>
    </TelemetryModules>
    ```

***Nepovedlo se upgradovat správně, může to vést k neočekávaným výjimkám nebo telemetrie neshromažďujících.***


## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>V aplikaci Visual Studio není možnost Přidat Application Insights.
*Když kliknu pravým tlačítkem na existující projekt v Průzkumník řešení, nevidím žádné možnosti Application Insights.*

* V nástrojích nejsou podporovány všechny typy projektů .NET. Webové a WCF projekty jsou podporovány. Pro jiné typy projektů, jako jsou například aplikace pro stolní nebo služby, můžete [Přidat sadu Application Insights SDK do projektu ručně](./windows-desktop.md).
* Ujistěte se, že máte [Visual Studio 2013 Update 3 nebo novější](/visualstudio/releasenotes/vs2013-update3-rtm-vs). Je součástí předinstalovaného nástroje pro vývojáře, které poskytují sadu Application Insights SDK.
* Vyberte **nástroje**, **rozšíření a aktualizace** a ověřte, že je nainstalovaná a povolená **Developer Analytics Tools** . Pokud ano, kliknutím na **aktualizace** ověřte, zda je k dispozici aktualizace.
* Otevřete dialogové okno Nový projekt a vyberte ASP.NET webová aplikace. Pokud se zobrazí možnost Application Insights, jsou nástroje nainstalovány. V takovém případě zkuste odinstalovat a znovu nainstalovat Developer Analytics Tools.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Přidání Application Insights se nezdařilo
*Při pokusu o přidání Application Insights do existujícího projektu se zobrazí chybová zpráva.*

Pravděpodobná příčina:

* Komunikace s portálem Application Insights se nezdařila; ani
* Došlo k nějakému problému s vaším účtem Azure;
* Máte [přístup jen pro čtení k předplatnému nebo skupině, ve které jste se pokusili vytvořit nový prostředek](./resources-roles-access-control.md).

Opravit

* Ověřte, že jste pro správný účet Azure zadali přihlašovací údaje pro přihlášení.
* V prohlížeči ověřte, zda máte přístup k [Azure Portal](https://portal.azure.com). Otevřete nastavení a podívejte se, jestli existuje nějaké omezení.
* [Přidejte Application Insights do existujícího projektu](./asp-net.md): v Průzkumník řešení klikněte pravým tlačítkem na projekt a vyberte Přidat Application Insights.

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Zobrazila se chybová zpráva klíč instrumentace nemůže být prázdný.
Vypadá to, že při instalaci Application Insights nebo možná protokolovacího adaptéru došlo k chybě.

V Průzkumník řešení klikněte pravým tlačítkem myši na projekt a vyberte možnost **Application Insights > konfigurovat Application Insights**. Zobrazí se dialogové okno, které vás vyzve, abyste se přihlásili do Azure a vytvořili prostředek Application Insights, nebo znovu použít už existující.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a> Na mém serveru sestavení chybí tento počet balíčků NuGet:
*Při ladění na svém vývojovém počítači se všechno vytvoří, ale na serveru sestavení se zobrazí chyba NuGet.*

Přečtěte si prosím [balíček NuGet obnovení](https://docs.nuget.org/Consume/Package-Restore) a [Automatické obnovení balíčků](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Chybějící příkaz nabídky pro otevření Application Insights ze sady Visual Studio
*Po kliknutí pravým tlačítkem myši na Průzkumník řešení projektu se nezobrazí žádné příkazy Application Insights ani se nezobrazuje otevřený Application Insights příkaz.*

Pravděpodobná příčina:

* Pokud jste vytvořili prostředek Application Insights ručně, nebo pokud je projekt typu, který nástroj Application Insights Tools nepodporuje.
* Analytické nástroje pro vývojáře jsou v aplikaci Visual Studio zakázané.
* Vaše Visual Studio je starší než 2013 aktualizace 3.

Opravit

* Ujistěte se, že verze sady Visual Studio je 2013 Update 3 nebo novější.
* Vyberte **nástroje**, **rozšíření a aktualizace** a ověřte, že jsou nainstalované a povolené **vývojářské analytické nástroje** . Pokud ano, kliknutím na **aktualizace** ověřte, zda je k dispozici aktualizace.
* V Průzkumník řešení klikněte pravým tlačítkem na svůj projekt. Pokud se zobrazí příkaz **Application Insights > nakonfigurovat Application Insights**, použijte ho k připojení projektu k prostředku ve službě Application Insights.

V opačném případě váš typ projektu není přímo podporován analytickými nástroji pro vývojáře. Pokud chcete zobrazit telemetrii, přihlaste se k [Azure Portal](https://portal.azure.com), zvolte Application Insights na levém navigačním panelu a vyberte svou aplikaci.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>Přístup byl odepřen při otevírání Application Insights ze sady Visual Studio.
*Příkaz nabídky otevřít Application Insights přejde na Azure Portal, ale zobrazí se chybová zpráva přístup byl odepřen.*

Přihlášení Microsoftu, které jste naposledy použili ve výchozím prohlížeči, nemá přístup k [prostředku, který byl vytvořen při přidání Application Insights do této aplikace](./asp-net.md). Existují dva nejpravděpodobnější důvody:

* Máte více než jednu účet Microsoft – možná pracovní a osobní účet Microsoft? Přihlášení, které jste naposledy použili ve výchozím prohlížeči, bylo pro jiný účet, než je ten, který má přístup k [přidání Application Insights do projektu](./asp-net.md).
  * Oprava: klikněte na své jméno v pravém horním rohu okna prohlížeče a odhlaste se. Pak se přihlaste pomocí účtu, který má přístup. Pak na levém navigačním panelu klikněte na Application Insights a vyberte svou aplikaci.
* Někdo jiný přidal do projektu Application Insights a zapomněl vám mu udělit [přístup ke skupině prostředků](./resources-roles-access-control.md) , ve které byl vytvořen.
  * Oprava: Pokud používali účet organizace, může vás přidat do týmu. nebo vám může udělit individuální přístup ke skupině prostředků.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>Asset nebyl nalezen při otevírání Application Insights ze sady Visual Studio.
*Příkaz nabídky otevřít Application Insights přejde na Azure Portal, ale zobrazí se chyba "Asset not found".*

Pravděpodobná příčina:

* Prostředek Application Insights pro vaši aplikaci byl odstraněn. ani
* Klíč instrumentace se nastavil nebo změnil v ApplicationInsights.config úpravou přímo, aniž by se musel aktualizovat soubor projektu.

Klíč instrumentace v ApplicationInsights.config řídí, kde se telemetrie posílá. Řádek v souboru projektu řídí, který prostředek je otevřen při použití příkazu v aplikaci Visual Studio.

Opravit

* V Průzkumník řešení klikněte pravým tlačítkem myši na projekt a vyberte možnost Application Insights, konfigurace Application Insights. V dialogovém okně můžete buď Odeslat telemetrii do existujícího prostředku, nebo vytvořit novou. Ani
* Otevřete prostředek přímo. Přihlaste se k [Azure Portal](https://portal.azure.com), na levém navigačním panelu klikněte na Application Insights a pak vyberte svou aplikaci.

## <a name="where-do-i-find-my-telemetry"></a>Kde najdu telemetrii?
*Jsem jste přihlášeni k [portál Microsoft Azure](https://portal.azure.com)a na domovském řídicím panelu Azure se díváte. Kde najdu Application Insights data?*

* V levém navigačním panelu klikněte na Application Insights a pak na název vaší aplikace. Pokud nemáte žádné projekty, je nutné [Přidat nebo nakonfigurovat Application Insights ve webovém projektu](./asp-net.md).  
  Zobrazí se některé souhrnné grafy. Kliknutím na ně můžete zobrazit další podrobnosti.
* V aplikaci Visual Studio při ladění aplikace klikněte na tlačítko Application Insights.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a> Žádná data serveru (nebo žádná data vůbec)
*Spustil (a) jsem aplikaci Application Insights v Microsoft Azure, ale v grafu se zobrazila informace o tom, jak shromažďovat... nebo není nakonfigurováno.* Nebo, *jenom zobrazení stránky a uživatelská data, ale žádná data serveru.*

* Spusťte aplikaci v režimu ladění v aplikaci Visual Studio (F5). Použijte aplikaci, aby se vygenerovala nějaká telemetrie. Zkontrolujte, zda jsou události zaznamenané v okně výstup sady Visual Studio.  
  ![Snímek obrazovky, který ukazuje spuštění aplikace v režimu ladění v aplikaci Visual Studio.](./media/asp-net-troubleshoot-no-data/output-window.png)
* Na portálu Application Insights otevřete [diagnostické vyhledávání](./diagnostic-search.md). Data se obvykle zobrazují jako první.
* Klikněte na tlačítko Aktualizovat. Okno se pravidelně aktualizuje, ale můžete ho také provést ručně. Interval aktualizace je delší pro větší časové rozsahy.
* Ověřte, zda se klíče instrumentace shodují. V hlavním okně aplikace na portále Application Insights v rozevíracím seznamu **základy** vyhledejte **klíč instrumentace**. Potom v projektu v aplikaci Visual Studio otevřete ApplicationInsights.config a vyhledejte `<instrumentationkey>` . Ověřte, zda jsou oba klíče stejné. Pokud ne:  
  * Na portálu klikněte na Application Insights a vyhledejte prostředek aplikace se správným klíčem. ani
  * V aplikaci Visual Studio Průzkumník řešení klikněte pravým tlačítkem myši na projekt a vyberte možnost Application Insights, konfigurovat. Resetujte aplikaci, aby odesílala telemetrii do správného prostředku.
  * Pokud nemůžete najít odpovídající klíče, ověřte, že používáte stejné přihlašovací údaje pro přihlášení v aplikaci Visual Studio jako v portálu.
* Na [řídicím panelu domů Microsoft Azure](https://portal.azure.com)se podívejte na mapu Service Health. Pokud se zobrazí upozornění, počkejte, až se vrátí do OK, a pak zavřete a znovu otevřete okno aplikace Application Insights.
* Projděte si také [náš stavový blog](https://techcommunity.microsoft.com/t5/azure-monitor-status/bg-p/AzureMonitorStatusBlog).
* Napsali jste jakýkoliv kód pro [sadu SDK na straně serveru](./api-custom-events-metrics.md) , která by mohla změnit klíč instrumentace v `TelemetryClient` instancích nebo v `TelemetryContext` ? Nebo jste napsali [konfiguraci filtru nebo vzorkování](./api-filtering-sampling.md) , které by mohly vyfiltrovat příliš mnoho?
* Pokud jste ApplicationInsights.config upravovali, pečlivě zkontrolujte konfiguraci [TelemetryInitializers a TelemetryProcessors](./api-filtering-sampling.md). Nesprávně pojmenovaný typ nebo parametr může způsobit, že sada SDK neposílá žádná data.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Žádná data v zobrazeních stránky, v prohlížečích, použití
*Zobrazují se data v grafech doba odezvy serveru a požadavky na server, ale neexistují žádná data v době načítání zobrazení stránky nebo v okně prohlížeče nebo použití.*

Data pocházejí ze skriptů na webových stránkách. 

* Pokud jste přidali Application Insights k existujícímu webovému projektu, [je nutné přidat skripty ručně](./javascript.md).
* Ujistěte se, že aplikace Internet Explorer nezobrazuje web v režimu kompatibility.
* Použijte funkci ladění v prohlížeči (F12 na některých prohlížečích a pak zvolte síť), abyste ověřili, že se data odesílají do `dc.services.visualstudio.com` .

## <a name="no-dependency-or-exception-data"></a>Žádná data o závislostech nebo výjimkách
Viz [telemetrie závislostí](./asp-net-dependencies.md) a [telemetrie výjimek](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Žádná data o výkonu
Údaje o výkonu (CPU, rychlost v/v a tak dále) jsou k dispozici pro [webové služby Java](./java-collectd.md), [desktopové aplikace Windows](./windows-desktop.md), [webové aplikace a služby IIS, pokud nainstalujete monitorování stavu](./monitor-performance-live-website-now.md)a [Azure Cloud Services](./app-insights-overview.md). najdete ho v části nastavení, servery.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Žádná data (serveru) od publikování aplikace na mém serveru
* Ověřte, že jste ve skutečnosti zkopírovali všechny společnosti Microsoft. ApplicationInsights knihovny DLL do serveru společně s Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* V bráně firewall možná budete muset [otevřít některé porty TCP](./ip-addresses.md).
* Pokud k odeslání z vaší podnikové sítě používáte proxy server, nastavte [defaultProxy](/previous-versions/dotnet/netframework-1.1/aa903360(v=vs.71)) v Web.config
* Windows Server 2008: Ujistěte se, že máte nainstalované následující aktualizace: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://web.archive.org/web/20150129090641/http://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Zobrazil (a) jsem data, ale zastavila se
* Dosáhli jste měsíční kvóty datových bodů? Pokud chcete zjistit, otevřete nastavení/kvótu a ceny. Pokud ano, můžete upgradovat svůj plán nebo platit za další kapacitu. Podívejte se na téma [cenové schéma](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Nezobrazují se všechna data, která očekávám
Pokud vaše aplikace odešle velké množství dat a používáte sadu Application Insights SDK pro ASP.NET verze 2.0.0-beta3 nebo novější, může funkce [adaptivního vzorkování](./sampling.md) fungovat a odesílat pouze procento telemetrie.

Můžete ho zakázat, ale nedoporučuje se to. Vzorkování je navrženo tak, aby související telemetrie byla správně odeslána pro účely diagnostiky.

## <a name="client-ip-address-is-0000"></a>IP adresa klienta je 0.0.0.0.

Od února 5 2018 jsme oznámili, že jsme odebrali protokolování IP adresy klienta. To nemá vliv na geografickou polohu.

> [!NOTE]
> Pokud potřebujete první 3 oktety IP adresy, můžete k přidání vlastního atributu použít [inicializátor telemetrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) .
> Toto nemá vliv na data shromážděná před 5. únorem 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Nesprávná geografická data v telemetrie uživatelů
Dimenze město, oblast a země jsou odvozeny z IP adres a nejsou vždy přesné. Tyto IP adresy se zpracují pro první místo a pak se změní na 0.0.0.0, aby se uložily.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Výjimka „metoda nebyla nalezena“ při spuštění v Azure Cloud Services
Vytvořili jste sestavení pro .NET 4.6? Verze 4.6 není v rolích Azure Cloud Services podporována automaticky. Před spuštěním aplikace [nainstalujte pro každou roli verzi 4.6](../../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="troubleshooting-logs"></a>Řešení potíží s protokoly

Podle těchto pokynů zaznamenejte protokoly řešení potíží pro vaše rozhraní.

### <a name="net-framework"></a>.NET Framework

1. Nainstalujte balíček [Microsoft. ASPNET. ApplicationInsights. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) z NuGet. Verze, kterou nainstalujete, se musí shodovat s aktuálně nainstalovanou verzí nástroje. `Microsoft.ApplicationInsighs`

2. Upravte soubor applicationinsights.config tak, aby zahrnoval následující:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.FileDiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Vaše aplikace musí mít oprávnění k zápisu do nakonfigurovaného umístění.

3. Restartujte proces, aby se tato nová nastavení vybrala sadou SDK.

4. Po dokončení tyto změny vraťte.

### <a name="net-core"></a>.NET Core

1. Nainstalujte [balíček NuGet sady Application Insights SDK pro ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) balíček z NuGet. Verze, kterou nainstalujete, musí odpovídat aktuální nainstalované verzi nástroje `Microsoft.ApplicationInsights` .

   Nejnovější verze Microsoft. ApplicationInsights. AspNetCore je 2.14.0 a odkazuje na Microsoft. ApplicationInsights verze 2.14.0. Proto by měla být verze Microsoft. ApplicationInsights. AspNetCore, která se má nainstalovat, 2.14.0.

2. Upravte `ConfigureServices` metodu ve `Startup.cs` třídě.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Vaše aplikace musí mít oprávnění k zápisu do nakonfigurovaného umístění.

3. Restartujte proces, aby se tato nová nastavení vybrala sadou SDK.

4. Po dokončení tyto změny vraťte.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a> Shromažďování protokolů pomocí PerfView
[PerfView](https://github.com/Microsoft/perfview) je bezplatný nástroj pro diagnostiku a analýzu výkonu, který vám pomůže izolovat procesor, paměť a další problémy tím, že shromažďuje a vizualizuje diagnostické informace z mnoha zdrojů.

Protokol Application Insights SDK s protokolem EventSource s protokolem, který může zachytit protokol PerfView.

Pokud chcete shromažďovat protokoly, Stáhněte si PerfView a spusťte tento příkaz:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-LoggerProvider,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Tyto parametry můžete upravit podle potřeby:
- **MaxCollectSec**. Nastavením tohoto parametru zabráníte spuštění PerfView na neomezenou dobu a vlivu na výkon serveru.
- **OnlyProviders**. Nastavte tento parametr tak, aby shromáždil jenom protokoly ze sady SDK. Tento seznam můžete přizpůsobit podle konkrétního šetření. 
- **NoGui**. Nastavte tento parametr pro shromažďování protokolů bez grafického uživatelského rozhraní.


Další informace
- [Zaznamenávání trasování výkonu pomocí PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Application Insights zdroje událostí](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/ETW)

## <a name="collect-logs-with-dotnet-trace"></a>Shromažďování protokolů pomocí dotnet – trasování

[`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace)Pro shromažďování protokolů, které mohou další pomoc při řešení potíží, taky zákazníci můžou použít nástroj .NET Core pro různé platformy. To může být zvláště užitečné pro prostředí založená na systému Linux.

Po instalaci nástroje [`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace) Spusťte níže uvedený příkaz v bash.

```bash
dotnet-trace collect --process-id <PID> --providers Microsoft-ApplicationInsights-Core,Microsoft-ApplicationInsights-Data,Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,Microsoft-ApplicationInsights-Extensibility-DependencyCollector,Microsoft-ApplicationInsights-Extensibility-HostingStartup,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,Microsoft-ApplicationInsights-Extensibility-Web,Microsoft-ApplicationInsights-Extensibility-WindowsServer,Microsoft-ApplicationInsights-WindowsServer-Core,Microsoft-ApplicationInsights-LoggerProvider,Microsoft-ApplicationInsights-Extensibility-EventSourceListener,Microsoft-ApplicationInsights-AspNetCore
```

## <a name="how-to-remove-application-insights"></a>Jak odebrat Application Insights

Naučte se, jak odebrat Application Insights v aplikaci Visual Studio podle kroků uvedených v [článku](./remove-application-insights.md)o odebrání.

## <a name="still-not-working"></a>Pořád nepracujeme...
* [Microsoft Q&Stránka s otázkou pro Application Insights](/answers/topics/azure-monitor.html)
