---
title: Azure Application Insights Snapshot Debugger pro aplikace .NET | Dokumentace Microsoftu
description: Ladění snímků se automaticky shromažďují, pokud jsou výjimky vyvolány v produkčních aplikacích .NET
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2018
ms.reviewer: pharring
ms.author: mbullwin
ms.openlocfilehash: 6dd39fddd99f5f8ea9329f21c271ed4c1063362d
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078966"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Ladicí snímky pro výjimky v aplikacích .NET

Když dojde k výjimce, můžete automaticky shromažďovat snímky ladění z vaší živé webové aplikace. Snímek zobrazuje stav zdrojového kódu a proměnné v okamžiku, kdy byla vyvolána výjimka. Snapshot Debugger (preview) v [Azure Application Insights](app-insights-overview.md) monitoruje telemetrie výjimek z vaší webové aplikace. Shromažďuje snímky na vaše horní vyvolání výjimky, abyste měli informace, které potřebujete k diagnostice problémů v produkčním prostředí. Zahrnout [balíček NuGet Snapshot collector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) ve vaší aplikaci a volitelně nakonfigurovat kolekci parametrů v [soubor ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Snímky se zobrazí na [výjimky](app-insights-asp-net-exceptions.md) na portálu Application Insights.

Snímky ladění můžete zobrazit na portálu a podívat se do zásobníku volání a zkontrolovat proměnné v každém rámci zásobníku volání. Chcete-li získat více výkonné možnosti ladění se zdrojovým kódem, otevřete snímky pomocí sady Visual Studio 2017 Enterprise ve [stahování rozšíření pro Snapshot Debugger pro sadu Visual Studio](https://aka.ms/snapshotdebugger). V sadě Visual Studio, můžete také [nastavit snímkovací body interaktivně pořizovat snímky](https://aka.ms/snappoint) bez čekání na výjimku.

Ladění snímky se ukládají po dobu sedmi dní. Tyto zásady uchování je nastavena na základě jednotlivých aplikací. Pokud je potřeba tuto hodnotu zvýšit, můžete požádat o zvýšení tak, že otevřete případ podpory na webu Azure Portal.

Shromažďování snímků je k dispozici pro:
* Aplikace rozhraní .NET framework a ASP.NET rozhraním .NET Framework 4.5 nebo novější.
* Aplikace .NET core 2.0 a ASP.NET Core 2.0 běžící na Windows.

Podporují se následující prostředí:
* Azure App Service.
* Cloudovou službu Azure používají operačního systému řady 4 nebo novější.
* Služby Azure Service Fabric spuštěná v systému Windows Server 2012 R2 nebo novější.
* Azure Virtual Machines s Windows serverem 2012 R2 nebo novější.
* Místní virtuální nebo fyzické počítače s Windows serverem 2012 R2 nebo novější.

> [!NOTE]
> Klientské aplikace (například WPF, Windows Forms nebo UPW) nejsou podporovány.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurace shromažďování snímků pro aplikace ASP.NET

1. [Povolit Application Insights ve webové aplikaci](app-insights-asp-net.md), pokud jste to ještě neudělali.

2. Zahrnout [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) balíčku NuGet ve vaší aplikaci.

3. Zkontrolujte výchozí možnosti, které balíček přidány do [soubor ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>1</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Snímky se shromažďují pouze na výjimky, které se hlásí do Application Insights. V některých případech (například starší verze platformy .NET), možná budete muset [Konfigurovat shromažďování výjimek](app-insights-asp-net-exceptions.md#exceptions) pro zobrazení výjimek pomocí snímků na portálu.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Konfigurace shromažďování snímků pro aplikace ASP.NET Core 2.0

1. [Povolit Application Insights ve webové aplikaci ASP.NET Core](app-insights-asp-net-core.md), pokud jste to ještě neudělali.

    > [!NOTE]
    > Být jisti, že vaše aplikace odkazuje na verzi 2.1.1 nebo novější, Microsoft.ApplicationInsights.AspNetCore balíčku.

2. Zahrnout [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) balíčku NuGet ve vaší aplikaci.

3. Upravit svou aplikaci `Startup` třídy přidání a konfigurace procesoru telemetrie Snapshot Collector.

    Přidejte následující příkazy using do `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Přidejte následující `SnapshotCollectorTelemetryProcessorFactory` třídu `Startup` třídy.

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    Přidat `SnapshotCollectorConfiguration` a `SnapshotCollectorTelemetryProcessorFactory` služby pro spuštění kanálu:

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Nakonfigurujte Snapshot Collector přidáním části SnapshotCollectorConfiguration do souboru appsettings.json. Příklad:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurace shromažďování snímků pro jiné aplikace .NET

1. Pokud vaše aplikace není již instrumentována pomocí nástroje Application Insights, začněte tím, že [povolením Application Insights a že Instrumentační klíč nastavíte](app-insights-windows-desktop.md).

2. Přidat [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) balíčku NuGet ve vaší aplikaci.

3. Snímky se shromažďují pouze na výjimky, které se hlásí do Application Insights. Budete muset změnit váš kód pro nahlášení je. Kód zpracování výjimek závisí na struktuře aplikace, ale příkladem je nižší než:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="grant-permissions"></a>Udělení oprávnění

Přístup k snímků je chráněn řízení přístupu na základě role (RBAC). Ke kontrole snímku, je musíte nejprve přidat do potřebné role vlastník předplatného.

> [!NOTE]
> Vlastníci a přispěvatelé automaticky není nutné tuto roli. Aby bylo možné zobrazit snímky, musí si sami přidají do role.

Přiřaďte vlastníky těchto předplatných `Application Insights Snapshot Debugger` role pro uživatele, kteří budou kontrolovat snímky. Tato role může být přiřazena na jednotlivé uživatele nebo skupiny podle vlastníků předplatných pro cílový prostředek Application Insights nebo jeho skupina prostředků nebo předplatného.

1. Přejděte do prostředku Application Insights na webu Azure Portal.
1. Klikněte na tlačítko **řízení přístupu (IAM)**.
1. Klikněte na tlačítko **+ přidat** tlačítko.
1. Vyberte **Application Insights Snapshot debuggeru** z **role** rozevíracího seznamu.
1. Vyhledejte a zadejte název pro uživatele přidat.
1. Klikněte na tlačítko **Uložit** tlačítko pro přidání uživatele do role.


> [!IMPORTANT]
> Snímky mohou obsahovat osobní a dalších citlivých informací v hodnoty proměnných a parametrů.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Ladění snímků na portálu Application Insights

Pokud je k dispozici pro danou výjimku nebo ID problému, snímku **otevřít snímek ladění** tlačítko se zobrazí na [výjimka](app-insights-asp-net-exceptions.md) na portálu Application Insights.

![Tlačítko Otevřít snímek ladění výjimek](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

V zobrazení ladění snímku uvidíte zásobník volání a podokno proměnných. Když vyberete rámce zásobníku volání v panelu zásobníku volání, můžete zobrazovat místní proměnné a parametry pro tuto funkci volat v podokně proměnné.

![Zobrazit snímek ladění na portálu](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Snímky mohou obsahovat citlivé informace a ve výchozím nastavení nejsou možné zobrazit. Chcete-li zobrazit snímky, musíte mít `Application Insights Snapshot Debugger` role, které jsou vám přiřazeny.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Ladění snímků sady Visual Studio 2017 Enterprise
1. Klikněte na tlačítko **Stáhnout snímek** tlačítko a stáhněte si `.diagsession` soubor, který lze otevřít v sadě Visual Studio 2017 Enterprise.

2. Chcete-li otevřít `.diagsession` souboru, je nutné nejprve [stáhnout a nainstalovat rozšíření pro Snapshot Debugger pro sadu Visual Studio](https://aka.ms/snapshotdebugger).

3. Po otevření souboru snímku, zobrazí se stránka s minimálním výpisem ladění v sadě Visual Studio. Klikněte na tlačítko **ladění spravovaného kódu** pro spuštění ladění snímku. Snímek se otevře na řádek kódu, kde byla vyvolána výjimka, takže můžete ladit aktuální stav procesu.

    ![Zobrazit snímek ladění v sadě Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Stažený snímek zahrnuje všechny soubory symbolů, které se nacházejí na webovém serveru aplikace. Tyto soubory symbolů je potřeba přidružit data snímku se zdrojovým kódem. Pro aplikace služby App Service Ujistěte se, že má povolit nasazení symbolů při publikování webové aplikace.

## <a name="how-snapshots-work"></a>Jak fungují snímky

Snapshot Collector je implementovaný jako [procesoru Telemetrie Application Insights](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Po spuštění aplikace procesoru snímku kolektor Telemetrie se přidá do kanálu telemetrie vaší aplikace.
Pokaždé, když vaše aplikace volání [TrackException](app-insights-asp-net-exceptions.md#exceptions), ID problému z typu výjimky a hází metoda vypočítá Snapshot Collector.
Pokaždé, když vaše aplikace volá TrackException, hodnota čítače se zvýší pro odpovídající ID problému. Dosáhne-li čítač `ThresholdForSnapshotting` hodnotu, ID problému se přidá do kolekce plánu.

Snapshot Collector také sleduje výjimky, jako jsou vyvolány prostřednictvím přihlášení k odběru [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) událostí. Když se aktivuje tuto událost, ID problému výjimky je počítaný porovná s ID problému v plánu shromažďování dat
Pokud se zjistí shoda, je vytvořen snímek spuštěného procesu. Snímek je přiřazen jedinečný identifikátor a výjimky je označený tímto identifikátorem. Po vrácení obslužnou rutinu FirstChanceException vyvolané výjimky se zpracovává jako obvykle. Nakonec výjimku dosáhne metoda TrackException znovu, kde, společně s identifikátorem snímku se hlásí do Application Insights.

Hlavní proces bude pokračovat a obsluhuje provoz pro uživatele s malou přerušení. Mezitím snímku je předávána procesu uživatele Nahrávajícího snímku. Snímek uživatele Nahrávajícího minimálního výpisu vytvoří a nahraje ho do Application Insights spolu se soubory relevantní symbolů (PDB).

> [!TIP]
> - Proces snímku je pozastavené klon spuštěnému procesu.
> - Vytvoření snímku trvá asi 10 až 20 MS.
> - Výchozí hodnota pro `ThresholdForSnapshotting` 1. Toto je také minimální hodnota. Proto má aplikace aktivovat stejná výjimka **dvakrát** před vytvořením snímku.
> - Nastavte `IsEnabledInDeveloperMode` na hodnotu true, pokud chcete generovat snímky při ladění v sadě Visual Studio.
> - Vytvoření rychlost pořizování snímků je omezena `SnapshotsPerTenMinutesLimit` nastavení. Výchozí limit je, že jeden snímek každých deset minut.
> - Více než 50 snímky za den mohou být odeslány.

## <a name="current-limitations"></a>Aktuální omezení

### <a name="publish-symbols"></a>Publikovat symboly
Snapshot Debugger vyžaduje soubory symbolů na provozním serveru k dekódování proměnných a ladicího prostředí sady Visual Studio.
Verze 15.2 (nebo novější) sady Visual Studio 2017 publikuje symboly pro verzi sestavení ve výchozím nastavení, když se publikuje do služby App Service. V předchozích verzích, budete muset přidat následující řádek na svůj profil publikování `.pubxml` souboru tak, aby v režimu vydání jsou publikovány symboly:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Pro Azure Compute a ostatními typy, ujistěte se, že jsou soubory symbolů ve stejné složce hlavní aplikaci knihovny DLL (obvykle `wwwroot/bin`) nebo jsou k dispozici v aktuální cestě.

### <a name="optimized-builds"></a>Optimalizovaná sestavení
V některých případech se lokální proměnné nelze zobrazit, v sestaveních pro vydání z důvodu optimalizace, které se použijí kompilátorem JIT.
Ve službě Azure App Services, ale můžete Snapshot Collector deoptimize aktivační metody, které jsou součástí jeho plánu shromažďování dat.

> [!TIP]
> Ve službě App Service, jak získat podporu deoptimization nainstalujte rozšíření Application Insights Site Extension.

## <a name="troubleshooting"></a>Řešení potíží

Tyto tipy k vyřešení problémů s Snapshot Debugger.

### <a name="use-the-snapshot-health-check"></a>Použít kontrolu stavu snímku
Několik běžných problémů za následek otevřít snímek ladění nezobrazuje. Pomocí zastaralé Snapshot Collector, například dosažení denního limitu pro nahrávání; nebo možná snímku je právě trvá příliš dlouho k nahrání. Pomocí kontroly stavu. snímek s řešením běžných problémů.

V podokně výjimka trasování začátku do konce zobrazení, která vás ke kontrole stavu snímku je odkaz.

![Zadejte Kontrola stavu snímku](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

Interaktivní rozhraní jako chat hledá běžné problémy a provede vás a opravte je.

![Kontrola stavu](./media/app-insights-snapshot-debugger/healthcheck.png)

Pokud se problém nevyřeší, pak použijte Manuál ke následujících kroků pro řešení potíží.

### <a name="verify-the-instrumentation-key"></a>Ověřte, že Instrumentační klíč

Ujistěte se, že používáte správné Instrumentační klíč v publikované aplikaci. Instrumentační klíč je obvykle čtení ze souboru ApplicationInsights.config. Ověřte, že hodnota je stejná jako Instrumentační klíč pro prostředek služby Application Insights, který se zobrazí na portálu.

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgrade na nejnovější verzi balíčku NuGet.

Abyste měli jistotu, že používáte nejnovější verzi Microsoft.ApplicationInsights.SnapshotCollector pomocí Správce balíčků NuGet v sadě Visual Studio. Zpráva k vydání verze najdete v https://github.com/Microsoft/ApplicationInsights-Home/issues/167

### <a name="check-the-uploader-logs"></a>V protokolech uživatele nahrávajícího

Po vytvoření snímku na disk se vytvoří soubor minimálního výpisu (.dmp). Samostatné uživatele nahrávajícího procesu vytvoří tento minimální výpis soubor a nahraje ho, spolu s všechny přidružené soubory PDB do Application Insights Snapshot debuggeru úložiště. Po úspěšném odeslání minimálním výpisu je odstranit z disku. Na disku jsou uloženy soubory protokolu pro proces odeslání. Ve službě App Service environment najdete v těchto protokolech `D:\Home\LogFiles`. Použijte správu webu kudu pro službu App Service k vyhledání těchto souborů protokolu.

1. Otevřete aplikaci služby App Service na webu Azure Portal.
2. Klikněte na tlačítko **Rozšířené nástroje**, nebo vyhledejte **Kudu**.
3. Klikněte na tlačítko **Přejít**.
4. V **konzolou pro ladění** rozevíracího seznamu vyberte **CMD**.
5. Klikněte na tlačítko **LogFiles**.

Měli byste vidět alespoň jeden soubor s názvem, který začíná `Uploader_` nebo `SnapshotUploader_` a `.log` rozšíření. Klikněte na příslušnou položku, abyste si stáhli všechny soubory protokolů nebo otevřete v prohlížeči.
Název souboru obsahuje jedinečnou příponu, která identifikuje instanci služby App Service. Pokud vaše instance služby App Service je hostovaná na více než jednom počítači, existují samostatné soubory protokolu pro každý počítač. Když uživatele nahrávajícího zjistí nový soubor s minimálním výpisem, se zaznamená do souboru protokolu. Tady je příklad snímku úspěšné a nahrávání:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> Výše uvedený příklad je z balíčku Microsoft.ApplicationInsights.SnapshotCollector NuGet verzi 1.2.0. V dřívějších verzích uživatele nahrávajícího proces se nazývá `MinidumpUploader.exe` a v protokolu je méně podrobný.

V předchozím příkladu je Instrumentační klíč `c12a605e73c44346a984e00000000000`. Tato hodnota by měla odpovídat Instrumentační klíč pro vaši aplikaci.
Minimální výpis je přidružený k snímku s ID `139e411a23934dc0b9ea08a626db16c5`. Toto ID můžete použít později k vyhledání telemetrie související výjimek v Application Insights Analytics.

Uživatele nahrávajícího hledá nové soubory PDB o každých 15 minut. Tady je příklad:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Pro aplikace, která _nejsou_ hostované ve službě App Service, uživatele nahrávajícího protokoly jsou ve stejné složce jako mini výpisy: `%TEMP%\Dumps\<ikey>` (kde `<ikey>` je Instrumentační klíč).

### <a name="troubleshooting-cloud-services"></a>Řešení potíží s Cloud Services
Pro role v cloudových službách může být příliš nízká k uložení souborů s minimálním výpisem, což vede ke ztrátě snímků výchozí dočasné složky.
Místo potřeby závisí na celkové pracovní sada aplikace, a počet souběžných snímků.
Pracovní sada webová role ASP.NET 32 bitů je obvykle 200 MB až 500 MB.
Povolit pro alespoň dva souběžné snímky.
Například pokud vaše aplikace používá 1 GB celkového pracovní sady, by měl zajistíte, že je minimálně 2 GB místa na disku pro ukládání snímků.
Postupujte podle těchto kroků a nakonfigurujte roli vaší cloudové služby pomocí vyhrazené místní prostředek pro snímky.

1. Přidejte nový místní prostředek ke cloudové službě úpravou souboru definičních (.csdef) cloudové služby. V následujícím příkladu je definován prostředek s názvem `SnapshotStore` s velikostí 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Upravit vaše role při spuštění kód k přidání proměnné prostředí, která odkazuje na `SnapshotStore` místního prostředku. Rolí pracovních procesů, by měl přidat kód pro vaši roli `OnStart` metody:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Pro webové role (ASP.NET), kód má přidat do vaší webové aplikaci `Application_Start` metody:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Aktualizovat soubor ApplicationInsights.config vaše role k přepsání umístění dočasné složky používají `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

### <a name="overriding-the-shadow-copy-folder"></a>Přepsání složka stínové kopie

Při spuštění Snapshot Collector, pokusí se najít složku na disku, který je vhodný pro spuštění procesu uživatele Nahrávajícího snímku. Složka stínové kopie se nazývá zvolenou složku.

Snapshot Collector zkontroluje několik známých umístění, ujistěte se, že má oprávnění ke zkopírování binárních souborů uživatele Nahrávajícího snímku. Používají se následující proměnné prostředí:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- DATA APLIKACÍ
- TEMP

Pokud se nenašel vhodný složky, Snapshot Collector sestavy o tom, že chyba _"Nelze najít složku vhodný stínové kopie."_

Pokud kopie nezdaří, sestavy Snapshot Collector `ShadowCopyFailed` chyby.

Pokud uživatele nahrávajícího nejde spustit, Snapshot Collector sestavy `UploaderCannotStartFromShadowCopy` chyba. Tělo zprávy obsahuje často `System.UnauthorizedAccessException`. K této chybě obvykle dochází, protože je aplikace spuštěna pod účtem s omezenými oprávněními. Účet má oprávnění k zápisu do složky stínové kopie, ale nemá oprávnění k provádění kódu.

Vzhledem k tomu, že tyto chyby se obvykle dojít během spouštění, že budete obvykle následovat `ExceptionDuringConnect` o tom, že chyba _"Uživatele Nahrávajícího se nepodařilo spustit."_

Chcete-li vyřešit tyto chyby, můžete určit složky stínové kopie ručně přes `ShadowCopyFolder` možnosti konfigurace. Například pomocí souboru ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Nebo, pokud používáte aplikaci .NET Core appsettings.json:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Abyste mohli najít výjimky se snímky hledání pomocí služby Application Insights

Při vytváření snímku aktivační výjimky označené pomocí ID snímku. Toto ID snímku je dostupná jako vlastní vlastnost při telemetrie výjimek se hlásí do Application Insights. Pomocí **hledání** ve službě Application Insights, můžete najít všechny telemetrie s `ai.snapshot.id` vlastní vlastnosti.

1. Přejděte do prostředku Application Insights na webu Azure Portal.
2. Klikněte na tlačítko **hledání**.
3. Typ `ai.snapshot.id` textového pole pro vyhledávání a stiskněte Enter.

![Hledat telemetrii s ID snímku na portálu](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Pokud toto hledání nebyly vráceny žádné výsledky, pak žádné snímky se hlásí do Application Insights pro vaši aplikaci ve vybraném časovém rozsahu.

K vyhledání ID konkrétní snímek z nástroje odeslání protokolů, zadejte do vyhledávacího pole tímto Identifikátorem. Pokud nelze najít telemetrii pro snímek, o kterém víte, že byl odeslán, postupujte podle těchto kroků:

1. Zkontrolujte, že se díváte na správný prostředek Application Insights tak, že ověříte Instrumentační klíč.

2. Pomocí časové razítko od odeslání protokolů, upravte filtr časový rozsah vyhledávání pro tento časový rozsah.

Pokud stále nevidíte výjimka s tímto ID snímku, nebyl telemetrie výjimek hlásí do Application Insights. Tato situace může nastat, pokud vaše aplikace došlo k chybě po, jakou trvalo snímku, ale před oznámila telemetrie výjimek. V takovém případě v protokolech služby App Service v rámci `Diagnose and solve problems` zobrazíte, pokud došlo k neočekávané restartování nebo neošetřených výjimek.

### <a name="edit-network-proxy-or-firewall-rules"></a>Upravit pravidla proxy nebo brány firewall sítě

Pokud vaše aplikace připojuje k Internetu přes proxy server nebo brána firewall, budete muset upravit pravidla, aby vaše aplikace mohla komunikovat se službou Snapshot Debugger. Tady je [seznam IP adres a portů používaných Snapshot Debugger](app-insights-ip-addresses.md#snapshot-debugger).

## <a name="next-steps"></a>Další postup

* [Nastavit snímkovací body ve vašem kódu](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) získat snímky bez čekání na výjimku.
* [Diagnostika výjimek ve vašich webových aplikacích](app-insights-asp-net-exceptions.md) vysvětluje, jak zviditelnit více výjimek Application Insights.
* [Inteligentní zjišťování](app-insights-proactive-diagnostics.md) automaticky zjišťuje anomálie výkonu.
