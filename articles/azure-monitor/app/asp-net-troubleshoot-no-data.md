---
title: Řešení potíží s chybějícími daty v nástroji Application Insights pro .NET
description: Nezobrazuje te data v Azure Application Insights? Zkuste to tady.
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: 34fc51f8f656ec0f630bd984ac1b28fbaa5e4dae
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802582"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Poradce při potížích s žádnými daty – Přehledy aplikací pro jádro .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>Některé z mých telemetrie chybí
*V Application Insights vidím jenom zlomek událostí, které jsou generovány v mé aplikaci.*

* Pokud jste důsledně vidět stejný zlomek, je to pravděpodobně kvůli adaptivní [vzorkování](../../azure-monitor/app/sampling.md). Chcete-li to potvrdit, otevřete search (z okna přehledu) a podívejte se na instanci Požadavku nebo jiné události. V dolní části sekce vlastností klikněte na tlačítko "..." získat úplné informace o nemovitosti. Pokud počet požadavků > 1, vzorkování je v provozu.
* V opačném případě je možné, že pro cenový plán dosahujete [limitu rychlosti dat.](../../azure-monitor/app/pricing.md#limits-summary) Tyto limity se uplatňují za minutu.

*Dochází ke ztrátě dat náhodně.*

* Zkontrolujte, zda dochází ke ztrátě dat na [kanálu telemetrie](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)

* Zkontrolujte všechny známé problémy v [úložišti GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) kanálu telemetrie

*Dochází ke ztrátě dat v konzolové aplikaci nebo ve Webové aplikaci, když se aplikace chystá zastavit.*

* Kanál SDK udržuje telemetrii ve vyrovnávací paměti a odesílá je v dávkách. Pokud se aplikace vypíná, bude pravděpodobně nutné explicitně volat [Flush()](api-custom-events-metrics.md#flushing-data). Chování `Flush()` závisí na skutečném použitém [kanálu.](telemetry-channels.md#built-in-telemetry-channels)

## <a name="no-data-from-my-server"></a>Žádná data z mého serveru
*Nainstaloval jsem aplikaci na webový server a teď z ní nevidím žádnou telemetrii. Fungovalo to OK na mém dev stroji.*

* Pravděpodobně problém s firewallem. [Nastavte výjimky brány firewall pro Application Insights pro odesílání dat](../../azure-monitor/app/ip-addresses.md).
* Serveru IIS pravděpodobně chybí některé požadavky: Rozšiřitelnost rozhraní .NET 4.5 a ASP.NET 4.5.

*Na webový server jsem [nainstaloval nástroj Sledování stavu,](../../azure-monitor/app/monitor-performance-live-website-now.md) který sleduje stávající aplikace. Nevidím žádné výsledky.*

* Viz [Poradce při potížích s monitorováním stavu](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>V sadě Visual Studio není možnost Přidat přehledy aplikací
*Když v Průzkumníku řešení kliknu pravým tlačítkem myši na existující projekt, nevidím žádné možnosti Přehledů aplikací.*

* Ne všechny typy projektu .NET jsou podporovány nástroji. Webové a WCF projekty jsou podporovány. U jiných typů projektů, jako jsou desktopové aplikace nebo aplikace služby, můžete do [projektu přidat sadu Application Insights SDK ručně](../../azure-monitor/app/windows-desktop.md).
* Ujistěte se, že máte [Visual Studio 2013 Update 3 nebo novější](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). Dodává se s předinstalovanými nástroji Pro analýzu vývojářů, které poskytují sadu Application Insights SDK.
* Vyberte **Nástroje**, **Rozšíření a Aktualizace a** zkontrolujte, zda jsou nainstalovány a **povoleny nástroje analýzy vývojářů.** Pokud ano, klikněte na **Aktualizace** a zjistěte, jestli je k dispozici aktualizace.
* Otevřete dialogové okno Nový projekt a zvolte ASP.NET webovou aplikaci. Pokud se zde zobrazí možnost Application Insights, pak jsou nainstalovány nástroje. Pokud ne, zkuste odinstalovat a znovu nainstalovat nástroje pro analýzu vývojářů.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Přidání přehledů aplikací se nezdařilo.
*Při pokusu o přidání Application Insights do existujícího projektu se zobrazí chybová zpráva.*

Pravděpodobné příčiny:

* Komunikace s portálem Application Insights se nezdařila. Nebo
* Došlo k nějakému problému s vaším účtem Azure;
* Máte pouze [přístup pro čtení k předplatnému nebo skupině, kde jste se pokoušeli vytvořit nový prostředek](../../azure-monitor/app/resources-roles-access-control.md).

Opravit:

* Zkontrolujte, zda jste zadali přihlašovací údaje pro správný účet Azure.
* V prohlížeči zkontrolujte, zda máte přístup k [portálu Azure](https://portal.azure.com). Otevřete Nastavení a zjistěte, zda existuje nějaké omezení.
* [Přidání přehledů aplikací do existujícího projektu](../../azure-monitor/app/asp-net.md): V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a zvolte "Přidat přehledy aplikací".

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Zobrazuje se chyba "Klíč instrumentace nemůže být prázdný"
Vypadá to, že se něco pokazilo při instalaci Application Insights nebo adaptéru pro protokolování.

V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a zvolte **Application Insights > Konfigurovat přehledy aplikací**. Zobrazí se dialogové okno, které vás vyzve k přihlášení do Azure a buď vytvořit prostředek Application Insights, nebo znovu použít existující.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a>"NuGet package(s) are missing" na mém serveru sestavení
*Vše se staví OK, když jsem ladění na mém vývojovém počítači, ale dostanu chybu NuGet na serveru sestavení.*

Viz [NuGet Package Restore](https://docs.nuget.org/Consume/Package-Restore) and [Automatic Package Restore](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Chybějící příkaz nabídky pro otevření application insights z Visual Studia
*Když kliknu pravým tlačítkem myši na Průzkumník a řešení projektu, nevidím žádné příkazy Application Insights nebo příkaz Otevřít přehledy aplikací.*

Pravděpodobné příčiny:

* Pokud jste prostředek Application Insights vytvořili ručně nebo pokud je projekt typu, který nástroje Application Insights nepodporují.
* Nástroje Analýzy pro vývojáře jsou ve vašem Visual Studiu zakázány.
* Visual Studio je starší než 2013 Update 3.

Opravit:

* Ujistěte se, že vaše verze Sady Visual Studio je aktualizace 3 nebo novější 2013.
* Vyberte **Nástroje**, **Rozšíření a Aktualizace a** zkontrolujte, zda jsou nainstalovány a **povoleny nástroje Analýzy vývojářů.** Pokud ano, klikněte na **Aktualizace** a zjistěte, jestli je k dispozici aktualizace.
* Klikněte pravým tlačítkem myši na projekt v Průzkumníku řešení. Pokud se zobrazí příkaz **Application Insights > Konfigurovat přehledy aplikací**, použijte ho k připojení projektu k prostředku ve službě Application Insights.

V opačném případě není váš typ projektu přímo podporován nástroji Analýzy pro vývojáře. Pokud chcete zobrazit telemetrii, přihlaste se k [portálu Azure](https://portal.azure.com), na levém navigačním panelu zvolte Application Insights a vyberte aplikaci.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Přístup byl odepřen" při otevírání přehledů aplikací z visual studia
*Příkaz nabídky Otevřít přehledy aplikací mě přenese na portál Azure, ale zobrazí se chyba odepření přístupu.*

Přihlášení microsoftu, které jste naposledy použili ve výchozím prohlížeči, nemá přístup k [prostředku, který byl vytvořen při přidání Application Insights do této aplikace](../../azure-monitor/app/asp-net.md). Existují dva pravděpodobné důvody:

* Máte více než jeden účet Microsoft - možná práci a osobní účet Microsoft? Přihlášení, které jste naposledy použili ve výchozím prohlížeči, bylo pro jiný účet, než který má přístup k [přidání Application Insights do projektu](../../azure-monitor/app/asp-net.md).
  * Oprava: Klikněte na své jméno v pravém horním rohu okna prohlížeče a odhlaste se. Pak se přihlaste pomocí účtu, který má přístup. Potom na levém navigačním panelu klikněte na Application Insights a vyberte aplikaci.
* Někdo jiný přidal Application Insights do projektu a zapomněl vám poskytnout [přístup ke skupině prostředků,](../../azure-monitor/app/resources-roles-access-control.md) ve které byl vytvořen.
  * Oprava: Pokud použili účet organizace, mohou vás přidat do týmu; nebo vám mohou udělit individuální přístup ke skupině prostředků.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Asset not found" při otevírání přehledů aplikací z Visual Studia
*Příkaz nabídky Otevřít přehledy aplikací mě přenese na portál Azure, ale zobrazí se chyba "prostředek nebyl nalezen".*

Pravděpodobné příčiny:

* Prostředek Application Insights pro vaši aplikaci byl odstraněn. Nebo
* Klíč instrumentace byl nastaven nebo změněn v ApplicationInsights.config jeho přímou úpravou bez aktualizace souboru projektu.

Instrumentační klíč v ApplicationInsights.config určuje, kam je odeslána telemetrická data. Řádek v souboru projektu určuje, který prostředek je otevřen při použití příkazu v sadě Visual Studio.

Opravit:

* V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a zvolte Application Insights, Configure Application Insights . V dialogovém okně můžete buď zvolit odeslání telemetrie do existujícího prostředku, nebo vytvořit nový. Nebo:
* Otevřete prostředek přímo. Přihlaste se na [portál Azure](https://portal.azure.com)Portal , klikněte na panelu na levém navigačním panelu na Přehledy aplikací a vyberte aplikaci.

## <a name="where-do-i-find-my-telemetry"></a>Kde najdu telemetrii?
*Přihlásil jsem se k [portálu Microsoft Azure](https://portal.azure.com)a dívám se na domácí řídicí panel Azure. Tak kde najdu data Application Insights?*

* Na levém navigačním panelu klikněte na Application Insights a potom na název aplikace. Pokud tam nemáte žádné projekty, musíte [přidat nebo nakonfigurovat Application Insights ve vašem webovém projektu](../../azure-monitor/app/asp-net.md).  
  Tam uvidíte nějaké souhrnné grafy. Můžete kliknout na ně vidět více podrobností.
* V Sadě Visual Studio klikněte při ladění aplikace na tlačítko Přehledy aplikací.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a>Žádná data serveru (nebo žádná data vůbec)
*Spustil jsem aplikaci a pak jsem otevřel službu Application Insights v Microsoft Azure, ale ve všech grafech je vidět "Naučte se shromažďovat...". nebo 'Není nakonfigurováno.'* Nebo *pouze zobrazení stránky a uživatelská data, ale žádná data serveru.*

* Spusťte aplikaci v režimu ladění v sadě Visual Studio (F5). Použijte aplikaci tak, aby generovat některé telemetrie. Zkontrolujte, zda můžete zobrazit události zaznamenané ve výstupním okně sady Visual Studio.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* Na portálu Application Insights otevřete [Diagnostické hledání](../../azure-monitor/app/diagnostic-search.md). Data se obvykle zobrazují zde jako první.
* Klepněte na tlačítko Aktualizovat. Čepel se pravidelně obnovuje, ale můžete to udělat také ručně. Interval aktualizace je delší pro větší časové rozsahy.
* Zkontrolujte, zda se klávesy přístroje shodují. Na hlavním okně pro vaši aplikaci na portálu Application Insights v rozevíracím panelu **Essentials** se podívejte na **instrumentaci**. Potom v projektu v sadě Visual Studio otevřete ApplicationInsights.config a najděte `<instrumentationkey>`. Zkontrolujte, zda jsou oba klíče stejné. Pokud tomu tak není:  
  * Na portálu klikněte na Application Insights a vyhledejte prostředek aplikace se správným klíčem. Nebo
  * V Průzkumníku řešení Visual Studia klikněte pravým tlačítkem myši na projekt a zvolte Application Insights, Configure. Resetujte aplikaci a odesílejte telemetrická data do správného prostředku.
  * Pokud nemůžete najít odpovídající klíče, zkontrolujte, zda používáte stejné přihlašovací údaje v sadě Visual Studio jako v portálu.
* Na [domovském řídicím panelu Microsoft Azure](https://portal.azure.com)najemtevní tesek na mapě Stav služby. Pokud existují nějaké výstrahy, počkejte, dokud se vrátí do OK a zavřete a znovu otevřete okno aplikace Insights aplikace.
* Podívejte se také [na náš stav blog](https://blogs.msdn.microsoft.com/servicemap-status/).
* Napsali jste nějaký kód pro sadu [SDK na straně serveru,](../../azure-monitor/app/api-custom-events-metrics.md) který by mohl změnit klíč instrumentace v `TelemetryClient` instancích nebo v ? `TelemetryContext` Nebo jste [napsali konfiguraci filtru nebo vzorkování,](../../azure-monitor/app/api-filtering-sampling.md) která by mohla být příliš filtrující?
* Pokud jste upravili ApplicationInsights.config, pečlivě zkontrolujte konfiguraci [TelemetryInitializers a TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md). Nesprávně pojmenovaný typ nebo parametr může způsobit, že sada SDK neodešle žádná data.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Žádné údaje o zobrazení chození stránek, prohlížečích, použití
*V grafech Doba odezvy serveru a Požadavky na server se zobrazují data, ale žádná data v době načítání zobrazení stránky nebo v okně Prohlížeč nebo Použití.*

Data pocházejí ze skriptů na webových stránkách. 

* Pokud jste přidali Application Insights do existujícího webového projektu, [musíte přidat skripty ručně](../../azure-monitor/app/javascript.md).
* Zkontrolujte, zda aplikace Internet Explorer nezobrazuje web v režimu kompatibility.
* Pomocí funkce ladění prohlížeče (F12 v některých prohlížečích, pak zvolte síť) `dc.services.visualstudio.com`k ověření, zda jsou data odesílána do aplikace .

## <a name="no-dependency-or-exception-data"></a>Žádná data závislosti nebo výjimky
Viz [telemetrie závislostí](../../azure-monitor/app/asp-net-dependencies.md) a [telemetrie výjimek](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Žádná data o výkonu
Data o výkonu (cpu, rychlost vipo a tak dále) jsou k dispozici pro [webové služby Java](../../azure-monitor/app/java-collectd.md), [desktopové aplikace windows](../../azure-monitor/app/windows-desktop.md), webové aplikace a [služby IIS, pokud nainstalujete sledování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md), a [Cloudové služby Azure](../../azure-monitor/app/app-insights-overview.md). najdete ji v části Nastavení, Servery.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Od publikování aplikace na můj server nebyla data (server)
* Zkontrolujte, zda jste skutečně zkopírovali všechny Microsoft. Knihovny DLL ApplicationInsights na server spolu s souborem Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* V bráně firewall může být pravděpodobně možné [otevřít některé porty TCP](../../azure-monitor/app/ip-addresses.md).
* Pokud k odeslání podnikové sítě potřebujete použít proxy server, nastavte [výchozí proxy](https://msdn.microsoft.com/library/aa903360.aspx) v souboru Web.config.
* Windows Server 2008: Ujistěte se, že jste nainstalovali následující aktualizace: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Dříve jsem viděl data, ale zastavil se
* Zkontrolujte [stav blogu](https://blogs.msdn.com/b/applicationinsights-status/).
* Dosáhli jste své měsíční kvóty datových bodů? Otevřete nastavení / kvótu a ceny, abyste to zjistili. Pokud ano, můžete svůj plán upgradovat nebo zaplatit za dodatečnou kapacitu. Viz [cenový režim](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Nevidím všechna data, která očekávám
Pokud vaše aplikace odešle velké množství dat a používáte application insights SDK pro ASP.NET verze 2.0.0-beta3 nebo novější, [funkce adaptivnívzorkování](../../azure-monitor/app/sampling.md) může fungovat a odesílat pouze procento telemetrie.

Můžete jej zakázat, ale to se nedoporučuje. Vzorkování je navržentak, aby související telemetrie je správně přenášena, pro diagnostické účely.

## <a name="client-ip-address-is-0000"></a>Ip adresa klienta je 0.0.0.0

5. února 2018 jsme oznámili, že jsme odstranili protokolování IP adresy klienta. To nemá vliv na geografickou polohu.

> [!NOTE]
> Pokud potřebujete první 3 oktety IP adresy, můžete použít [telemetrický inicializátor](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer) k přidání vlastního atributu.
> Toto nemá vliv na data shromážděná před 5. únorem 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Špatná zeměpisná data v telemetrii uživatelů
Dimenze města, oblasti a země jsou odvozeny z IP adres a nejsou vždy přesné. Tyto IP adresy jsou nejprve zpracovány pro umístění a poté změněny na 0.0.0.0, které mají být uloženy.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Výjimka „metoda nebyla nalezena“ při spuštění v Azure Cloud Services
Vytvořili jste sestavení pro .NET 4.6? Verze 4.6 není v rolích Azure Cloud Services podporována automaticky. Před spuštěním aplikace [nainstalujte pro každou roli verzi 4.6](../../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="troubleshooting-logs"></a>Protokoly pro odstraňování potíží

Podle těchto pokynů zachyťte protokoly řešení potíží pro vaši architekturu.

### <a name="net-framework"></a>.NET Framework

1. Nainstalujte balíček [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) z NuGet. Instalovaná verze musí odpovídat aktuální nainstalované verzi`Microsoft.ApplicationInsighs`

2. Upravte soubor applicationinsights.config tak, aby zahrnoval následující:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Aplikace musí mít oprávnění k zápisu do nakonfigurovaného umístění.

3. Restartujte proces tak, aby tato nová nastavení převzala sada SDK

4. Po dokončení vraťte tyto změny zpět.

### <a name="net-core"></a>.NET Core

1. Nainstalujte balíček [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) z NuGet. Instalovaná verze musí odpovídat aktuální nainstalované verzi`Microsoft.ApplicationInsights`

Nejnovější verze Microsoft.ApplicationInsights.AspNetCore je 2.8.2 a odkazuje na Microsoft.ApplicationInsights verze 2.11.2. Proto by verze microsoft.aspNet.ApplicationInsights.HostingStartup, která má být nainstalována, měla být 2.11.2

2. Změnit `ConfigureServices` metodu `Startup.cs` ve vaší třídě.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Aplikace musí mít oprávnění k zápisu do nakonfigurovaného umístění.

3. Restartujte proces tak, aby tato nová nastavení převzala sada SDK

4. Po dokončení vraťte tyto změny zpět.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a>Sběr protokolů s PerfView
[PerfView](https://github.com/Microsoft/perfview) je bezplatný nástroj pro diagnostiku a analýzu výkonu, který pomáhá izolovat procesor, paměť a další problémy shromažďováním a vizualizací diagnostických informací z mnoha zdrojů.

Application Insights SDK protokolu EventSource vlastní řešení potíží protokoly, které mohou být zachyceny PerfView.

Chcete-li shromažďovat protokoly, stáhněte perfView a spusťte tento příkaz:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Tyto parametry můžete podle potřeby upravit:
- **MaxCollectSec**. Nastavte tento parametr, chcete-li zabránit spuštění perfView neomezeně dlouho a ovlivňuje výkon serveru.
- **OnlyProviders**. Tento parametr nastavte tak, aby shromažďoval pouze protokoly ze sady SDK. Tento seznam můžete přizpůsobit na základě konkrétních šetření. 
- **NoGui**. Nastavte tento parametr pro shromažďování protokolů bez grafického uživatelského rozhraní.


Další informace
- [Zaznamenávání sledování výkonu pomocí perfview](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Zdroje událostí Přehledy aplikací](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="how-to-remove-application-insights"></a>Jak odebrat přehledy aplikací

Zjistěte, jak odebrat Application Insights v sadě Visual Studio podle pokynů uvedených v [článku](../../azure-monitor/app/remove-application-insights.md)odebrání .

## <a name="still-not-working"></a>Pořád nefunguje...
* [Fórum Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
