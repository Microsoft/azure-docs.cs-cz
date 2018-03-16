---
title: "Azure Application Insights snímku ladicí program pro aplikace .NET | Microsoft Docs"
description: "Ladění snímky jsou shromažďovány automaticky, pokud jsou výjimky vyvolány v produkční aplikace .NET"
services: application-insights
documentationcenter: 
author: pharring
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: mbullwin
ms.openlocfilehash: 5a2b3dbce1d969eaa9937ad866fd055ae72e6529
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Ladění snímků výjimky v aplikacích .NET

Když dojde k výjimce, může automaticky shromažďovat snímku ladění z provozu webové aplikace. Snímek zobrazuje stav zdrojového kódu a proměnné v okamžiku, kdy byla výjimka vydána. Snímek ladicí program (preview) v [Azure Application Insights](app-insights-overview.md) monitoruje výjimka telemetrie z vaší webové aplikace. Shromažďuje snímky na vaše horní vyvolání výjimky, tak, aby informace, že potřebujete diagnostikovat problémy v produkčním prostředí. Zahrnout [balíček NuGet kolekce snímku](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) ve vaší aplikaci a volitelně nakonfigurujte parametry kolekce v [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Snímky zobrazí na [výjimky](app-insights-asp-net-exceptions.md) na portálu služby Application Insights.

Snímky ladění můžete zobrazit na portálu a podívat se do zásobníku volání a zkontrolovat proměnné v každém rámci zásobníku volání. Pokud chcete získat výkonnější ladění zkušeností se zdrojovým kódem, otevřete snímky s Visual Studio Enterprise 2017 podle [stahování ladicí program snímku rozšíření pro Visual Studio](https://aka.ms/snapshotdebugger). V sadě Visual Studio můžete také [nastavit Snappoints k pořízení snímků interaktivně](https://aka.ms/snappoint) bez čekání na výjimku.

Snímek kolekce je k dispozici pro:
* Aplikace rozhraní .NET framework a ASP.NET spuštění rozhraní .NET Framework 4.5 nebo novější.
* Aplikace rozhraní .NET 2.0 core a ASP.NET Core 2.0 v systému Windows.

Podporovány jsou následující prostředí:
* Azure App Service.
* Služba Azure Cloud spuštěna řada operačního systému, 4 nebo novější.
* Azure Service Fabric služby běžící na Windows Server 2012 R2 nebo novějším.
* Virtuální počítače Azure s Windows serverem 2012 R2 nebo novější.
* Místní virtuální nebo fyzické počítače se systémem Windows Server 2012 R2 nebo novější.

> [!NOTE]
> Klientské aplikace (například WPF, Windows Forms nebo UWP) nejsou podporovány.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurace shromažďování snímků pro aplikace ASP.NET

1. [Povolit Application Insights ve vaší webové aplikaci](app-insights-asp-net.md), pokud jste ho ještě neučinili.

2. Zahrnout [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) balíček NuGet v aplikaci. 

3. Zkontrolujte výchozí možnosti, které balíčku přidány do [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>24:00:00</ProblemCounterResetInterval>
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

4. Snímky jsou shromažďovány pouze na výjimky, které jsou hlášeny Application insights. V některých případech (například starší verze platformy .NET), možná budete muset [konfigurace shromažďování výjimek](app-insights-asp-net-exceptions.md#exceptions) pro zobrazení výjimek pomocí snímků na portálu.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Konfigurace shromažďování snímků pro technologii ASP.NET 2.0 základní aplikace

1. [Povolit Application Insights ve vaší webové aplikaci ASP.NET Core](app-insights-asp-net-core.md), pokud jste ho ještě neučinili.

    > [!NOTE]
    > Být jisti, že vaše aplikace odkazuje na verzi 2.1.1 nebo novější, Microsoft.ApplicationInsights.AspNetCore balíčku.

2. Zahrnout [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) balíček NuGet v aplikaci.

3. Úprava vaší aplikace `Startup` třída k přidání a konfiguraci kolekce snímku telemetrie procesoru.

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   ...
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

       public Startup(IConfiguration configuration) => Configuration = configuration;

       public IConfiguration Configuration { get; }

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

4. Nakonfigurujte snímek kolekce přidáním části SnapshotCollectorConfiguration do appSettings.JSON určený. Příklad:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": true,
       "ThresholdForSnapshotting": 5,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"24:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurace shromažďování snímků pro jiné aplikace rozhraní .NET

1. Pokud vaše aplikace není instrumentována již s nástrojem Application Insights, začít [povolení Application Insights a nastavení klíč instrumentace](app-insights-windows-desktop.md).

2. Přidat [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) balíček NuGet v aplikaci.

3. Snímky jsou shromažďovány pouze na výjimky, které jsou hlášeny Application insights. Potřebujete upravit kód k jejich sestavy. Kód zpracování výjimek závisí na struktuře vaší aplikace, ale zde je příklad:
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
    
## <a name="grant-permissions"></a>Udělit oprávnění

Vlastníky předplatného Azure můžete prohlédnout snímky. Ostatní uživatelé musí udělit oprávnění vlastníka.

Udělit oprávnění, přiřaďte mu `Application Insights Snapshot Debugger` role pro uživatele, kteří bude kontrolovat snímky. Tato role může být přiřazena na jednotlivé uživatele nebo skupiny s vlastníky předplatného pro cílový prostředek Application Insights nebo jeho skupinu prostředků nebo předplatného.

1. Otevřete okno řízení přístupu (IAM).
1. Klikněte tlačítko Přidat +.
1. Vyberte z rozevíracího seznamu rolí Application Insights snímku ladicí program.
1. Vyhledejte a zadejte název pro uživatele, přidání.
1. Klikněte na tlačítko Uložit přidejte uživatele k roli.


> [!IMPORTANT]
> Snímky mohou obsahovat osobní a dalších citlivých informací v proměnné a parametr hodnoty.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Ladění snímky v portálu služby Application Insights

Pokud snímek je k dispozici pro danou výjimky nebo ID problému, **otevřít ladění snímek** na se zobrazí tlačítko [výjimka](app-insights-asp-net-exceptions.md) na portálu služby Application Insights.

![Tlačítko Otevřít ladění snímku na výjimky](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

V zobrazení ladění snímků zobrazí zásobník volání a podokně proměnné. Když vyberete v podokně Zásobník volání rámce zásobníku volání, můžete zobrazit místní proměnné a parametry pro tuto funkci volání v podokně proměnné.

![Zobrazení ladění snímku na portálu](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Snímky mohou obsahovat citlivé informace a ve výchozím nastavení nejsou viditelná. Chcete-li zobrazit snímky, musíte mít `Application Insights Snapshot Debugger` přiřazená role.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Ladění snímky s Visual Studio 2017 Enterprise
1. Klikněte na tlačítko **stáhnout snímku** tlačítko Stáhnout `.diagsession` souboru, který lze otevřít v aplikaci Visual Studio Enterprise 2017. 

2. Chcete-li otevřít `.diagsession` souboru, je nutné nejprve [stáhnout a nainstalovat rozšíření ladicí program snímku pro sadu Visual Studio](https://aka.ms/snapshotdebugger).

3. Po otevření souboru snímku, zobrazí se stránka minimální výpis ladění v sadě Visual Studio. Klikněte na tlačítko **ladění spravovaného kódu** spustit ladění snímku. Snímek se otevře na řádek kódu, kde byla výjimka vydána tak, aby můžete ladit, aktuální stav procesu.

    ![Zobrazení ladění snímku v sadě Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Stažený snímku obsahuje symbol soubory, které nebyly nalezeny na vašem webovém serveru aplikace. Tyto soubory symbolů nutné přidružit data snímku se zdrojovým kódem. Aplikace služby App Service je nutné povolit nasazení symbol při publikování webové aplikace.

## <a name="how-snapshots-work"></a>Jak fungují snímky

Když se aplikace spustí, proces osoba samostatné snímku je vytvořen, který monitoruje vaše aplikace pro požadavky na snímku. Pokud se požaduje snímku, stínové kopie běžící proces se provádí v milisekundách 10 až 20. Proces stínové pak analýzy a snímku se vytvoří během procesu hlavní nadále provozuje a poskytovat provozu pro uživatele. Potom nahrání snímku do služby Application Insights společně s všechny relevantní symbolu (.pdb) soubory, které jsou potřebné k zobrazení snímku.

## <a name="current-limitations"></a>Aktuální omezení

### <a name="publish-symbols"></a>Publikování symboly
Snímek ladicí program vyžaduje soubory symbolů v provozním serveru k dekódování proměnné a pro poskytování zkušenosti s laděním v sadě Visual Studio. Symboly pro verzi sestavení 15.2 verzi Visual Studio 2017 publikuje ve výchozím nastavení, pokud tato možnost publikuje do služby App Service. V předchozích verzích, je nutné přidat následující řádek na svůj profil publikování `.pubxml` tak, aby symboly jsou publikovány v režimu vydání:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Pro Azure Compute a dalších typů, zajistěte, aby soubory symbolů byly ve stejné složce dll hlavní aplikace (obvykle `wwwroot/bin`) nebo jsou k dispozici v aktuální cestě.

### <a name="optimized-builds"></a>Optimalizované sestavení
V některých případech místní proměnné nelze zobrazit, v sestavení pro vydání z důvodu optimalizace, které se použijí během procesu vytváření.

## <a name="troubleshooting"></a>Řešení potíží

Tyto tipy pomáhají při řešení problémů s ladicím programem snímku.

### <a name="verify-the-instrumentation-key"></a>Ověřte klíč instrumentace

Ujistěte se, že používáte klíč instrumentace správné v k publikované aplikaci. Application Insights obvykle čte klíč instrumentace z soubor ApplicationInsights.config. Ověřte, že hodnota je stejná jako klíč instrumentace pro prostředek Application Insights, který se zobrazí na portálu.

### <a name="check-the-uploader-logs"></a>Zkontrolujte protokoly osoba

Po vytvoření snímku se vytvoří soubor s minimálním (.dmp) na disku. Proces samostatné osoba trvá tento soubor minimální výpis a odesílá, společně s všechny přidružené soubory PDB do úložiště Application Insights snímku ladicí program. Po úspěšném odeslání minimální výpis je odstraněn z disku. Na disku zůstanou zachovány soubory protokolů pro proces osoba. V prostředí služby App Service, můžete najít tyto protokoly v `D:\Home\LogFiles`. Použití serveru správy Kudu pro službu App Service k nalezení tyto soubory protokolu.

1. Otevřete aplikaci aplikační služby na portálu Azure.

2. Vyberte **Rozšířené nástroje** okno, nebo vyhledejte **Kudu**.
3. Klikněte na tlačítko **přejděte**.
4. V **konzolou pro ladění** rozevíracím seznamu vyberte **CMD**.
5. Klikněte na tlačítko **LogFiles**.

Měli byste vidět alespoň jeden soubor s názvem, který začíná `Uploader_` nebo `SnapshotUploader_` a `.log` rozšíření. Klikněte na příslušnou ikonu Stáhnout všechny soubory protokolů nebo je otevřít v prohlížeči.
Název souboru obsahuje jedinečnou příponu, který identifikuje instanci služby App Service. Pokud vaše instance služby App Service je hostitelem více než jeden počítač, nejsou samostatné soubory protokolu pro každý počítač. Když osoba zjistí nový soubor s minimálními výpisy, zaznamenává se v souboru protokolu. Tady je příklad úspěšné snímku a nahrávání:

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
> V předchozím příkladu je z verze 1.2.0 balíček Microsoft.ApplicationInsights.SnapshotCollector Nuget. V dřívějších verzích nástroje odeslání proces se označuje jako `MinidumpUploader.exe` a v protokolu je méně podrobných.

V předchozím příkladu je klíč instrumentace `c12a605e73c44346a984e00000000000`. Tato hodnota musí odpovídat klíč instrumentace pro vaši aplikaci.
Minimální výpis souvisí s snímku s ID `139e411a23934dc0b9ea08a626db16c5`. Toto ID můžete použít později k vyhledání telemetrie přidružené výjimek ve Application Insights Analytics.

Procesu pro načtení hledá nové soubory PDB o jednou za 15 minut. Tady je příklad:

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

Pro aplikace, které jsou _není_ hostované ve službě App Service, protokoly osoba jsou ve stejné složce jako minimálním výpisem: `%TEMP%\Dumps\<ikey>` (kde `<ikey>` je klíč instrumentace).

### <a name="troubleshooting-cloud-services"></a>Řešení potíží s cloudové služby
Rolí v cloudových služeb může být dočasné složce výchozí minimální výpis souborů, což ztraceny snímky příliš malá.
Místo potřeby závisí na celkový pracovní sady aplikace, a počet souběžných snímků.
Pracovní sady 32-bit webovou roli ASP.NET je obvykle mezi 200 MB a 500 MB.
Měli byste povolit pro alespoň dva souběžné snímky.
Například pokud vaše aplikace používá 1 GB celkový pracovní sady, měli byste zajistit, že je alespoň 2 GB místa na disku pro ukládání snímků.
Postupujte podle těchto kroků nakonfigurujete vaše cloudové služby role s vyhrazenou místní prostředek pro snímky.

1. Přidejte nové místní prostředek do cloudové služby tak, že upravíte soubor definice (.csdf) cloudové služby. V následujícím příkladu je definován prostředek s názvem `SnapshotStore` s velikostí 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Upravit vaše role při spuštění kódu k přidání proměnné prostředí, která odkazuje na `SnapshotStore` místní prostředek. U rolí pracovního procesu, měli přidat kód pro vaši roli `OnStart` metoda:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Pro webové role (ASP.NET), kód musí být přidaní do webové aplikace `Application_Start` metoda:
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

3. Aktualizovat soubor ApplicationInsights.config vaše role k přepsání umístění dočasné složky používané `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Výjimky se snímky hledat pomocí Application Insights

Když je snímek vytvořen, aktivační výjimky se označí s ID snímku. Kdy je telemetrie výjimek hlášena Application insights, zda jsou zahrnuty jako vlastní vlastnost ID snímku. V okně hledání Application Insights, můžete najít všechny telemetrická s `ai.snapshot.id` vlastní vlastnosti.

1. Procházejte do zdroje Application Insights na portálu Azure.
2. Klikněte na tlačítko **vyhledávání**.
3. Typ `ai.snapshot.id` textového vyhledávacího pole a stiskněte Enter.

![Vyhledejte telemetrie s ID snímku na portálu](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Pokud toto hledání nebyly vráceny žádné výsledky, pak žádné snímky byly hlášené Application insights pro aplikace v vybraný časový rozsah.

K vyhledání ID konkrétní snímek z nástroje odeslání protokolů, zadejte toto ID do vyhledávacího pole. Pokud nemůžete najít telemetrii pro snímek, který víte, že byl odeslán, postupujte takto:

1. Zkontrolujte, že se díváte na pravém prostředku Application Insights kontrolou klíč instrumentace.

2. Pomocí časové razítko z procesu pro načtení protokolu, upravte filtr časový rozsah hledání tak, aby pokrývalo tento časový rozsah.

Pokud stále nevidíte výjimku s tímto ID snímku, telemetrie výjimek nebyla hlášena do Application Insights. Tato situace může nastat, pokud vaše aplikace došlo k chybě po trvalo snímku, ale předtím, než ho hlášené telemetrie výjimek. V takovém případě zkontrolujte protokoly služby App Service v části `Diagnose and solve problems` chcete zobrazit, pokud byly neočekávané restartování nebo neošetřené výjimky.

## <a name="next-steps"></a>Další postup

* [Nastavit snappoints ve vašem kódu](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) získat snímky bez čekání na výjimku.
* [Diagnostikovat výjimky ve webových aplikacích](app-insights-asp-net-exceptions.md) vysvětluje, jak chcete zviditelnit více výjimek pro Application Insights. 
* [Inteligentní detekce](app-insights-proactive-diagnostics.md) automaticky vyhledá anomálie výkonu.
