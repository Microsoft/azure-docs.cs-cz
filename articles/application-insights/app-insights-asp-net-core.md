---
title: Azure Application Insights pro ASP.NET Core | Dokumentace Microsoftu
description: Monitorování webových aplikací pro dostupnost, výkon a využití.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 6635906b6aae418fa3754f1152fae3e0d8903ffc
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989753"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights pro ASP.NET Core

Azure Application Insights poskytuje podrobné monitorování webové aplikace na úrovni kódu. Můžete snadno monitorovat webové aplikace z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé.

Tento článek vás provede procesem vytvoření ukázky ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) aplikace ve Visual Studiu a jak začít monitorovat pomocí Azure Application Insights.

## <a name="prerequisites"></a>Požadavky

- Sady SDK .NET Core 2.0.0 nebo novější.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) verze 15.7.3 nebo novější s úlohou vývoj pro ASP.NET a web. 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Vytvoření projektu aplikace ASP.NET Core v sadě Visual Studio

1. Klikněte pravým tlačítkem a spusťte **Visual Studio 2017** jako správce.
2. Vyberte **souboru** > **nové** > **projektu** (Ctrl-Shift-N).

   ![Snímek obrazovky nabídky Nový projekt sady Visual Studio soubor](./media/app-insights-asp-net-core/001-new-project.png)

3. Rozbalte **Visual C#** > vyberte **.NET Core** > **webová aplikace ASP.NET Core**. Zadejte **název** > **název řešení** > zkontrolujte **vytvořit nové úložiště Git**.

   ![Snímek obrazovky sady Visual Studio souboru Průvodce vytvořením nového projektu](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Vyberte **.Net Core** > **ASP.NET Core 2.0** **webovou aplikaci** > **OK**.

    ![Snímek obrazovky sady Visual Studio souboru nového projektu výběr nabídky](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Hledání Application Insights

Ve výchozím nastavení v sadě Visual Studio 2015 Update 2 nebo novější s ASP.NET Core 2 + na základě projektu je verze můžete využít výhod [hledání Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio) ještě předtím, než je explicitně přidat Application Insights do projektu.

K otestování této funkce:

1. Spusťte aplikaci klepnutím na službu IIS Express ![Snímek obrazovky sady Visual Studio ikonu IIS Express](./media/app-insights-asp-net-core/004-iis-express.png)

2. Vyberte **zobrazení** > **ostatní Windows** > **hledání Application Insights**.

   ![Snímek obrazovky diagnostických nástrojů sady Visual Studio](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. Je momentálně dostupná pro místní analýzu pouze telemetrii relace ladění. Chcete-li plně povolte Application Insights, vyberte **připravenost Telemetrie** v horním rohu klikněte pravým tlačítkem myši, nebo postupujte podle kroků v další části.

   ![Snímek obrazovky sady Visual Studio Application Insights hledání](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Další informace o jak Visual Studio světla do funkce jako [hledání Application Insights](app-insights-visual-studio.md) a [CodeLens](app-insights-visual-studio-codelens.md) místně předtím, než jste přidali Application Insights do projektu ASP.NET Core podívejte se vysvětlení na [konci tohoto článku.](#Application-Insights-search-continued)

## <a name="add-application-insights-telemetry"></a>Přidání Telemetrie Application Insights

1. Vyberte **projektu** > **přidat Telemetrii Application Insights...** . (Nebo můžete kliknout pravým tlačítkem **připojené služby** a vyberte Přidat připojenou službu.)

    ![Snímek obrazovky sady Visual Studio souboru nového projektu výběr nabídky](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Vyberte **Začínáme**. (V závislosti na vaší verzi sady Visual Studio text může mírně lišit. Místo toho mají některé starší verze **začít zdarma** tlačítko.)

    ![Snímek obrazovky sady Visual Studio souboru nového projektu výběr nabídky](./media/app-insights-asp-net-core/008-get-started.png)

3. Vyberte odpovídající **předplatné** > **prostředků** > **zaregistrovat**.

## <a name="changes-made-to-your-project"></a>Změní Made do projektu

Application Insights je nízkou režii. Zkontrolovat změny do svého projektu tak, že přidáte telemetrii Application Insights:

Vyberte **zobrazení** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projektu** > **změny**

- Čtyři celkový počet změn:

  ![Snímek obrazovky se soubory změnit tak, že přidáte Application Insights](./media/app-insights-asp-net-core/009-changes.png)

- Vytvoření jednoho souboru:

   _ConnectedService.json_

```json
{
  "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
  "Version": "8.12.10405.1",
  "GettingStartedDocument": {
    "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
  }
}
```

- Tři soubory jsou upraveny: (Další poznámky přidané zvýrazněte změny)

  _appsettings.json_

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  },
// Changes to file post adding Application Insights Telemetry:
  "ApplicationInsights": {
    "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
  }
}
//
```

  _ContosoDotNetCore.csproj_

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
    <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
<!---->
  </PropertyGroup>
  <ItemGroup>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
<!---->
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
  </ItemGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
  </ItemGroup>
<!--Changes to file post adding Application Insights Telemetry:-->
  <ItemGroup>
    <WCFMetadata Include="Connected Services" />
  </ItemGroup>
<!---->
</Project>
```

   _Program.cs_

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
// Change to file post adding Application Insights Telemetry:
                .UseApplicationInsights()
//
                .UseStartup<Startup>()
                .Build();
    }
}
```

## <a name="synthetic-transactions-with-powershell"></a>Syntetické transakce s využitím Powershellu

Automatizace žádostí o aplikace s využitím syntetické transakce.

1. Spusťte aplikaci klepnutím na službu IIS Express ![Snímek obrazovky sady Visual Studio ikonu IIS Express](./media/app-insights-asp-net-core/004-iis-express.png)

2. Zkopírujte adresu url z adresního řádku prohlížeče. Je ve formátu `http://localhost:{random port number}`

   ![Snímek obrazovky z adresního řádku prohlížeče adresa url](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Spusťte následující smyčka prostředí PowerShell pro vytvoření 100 syntetické transakce s vaší aplikace pro testy. Změňte číslo portu po **localhost:** tak, aby odpovídala adrese url, které jste zkopírovali v předchozím kroku.

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Otevřít portál Application Insights

Po spuštění Powershellu z předchozí části, spusťte službu Application Insights do zobrazení transakcí a potvrďte, že se shromažďují data. 

V nabídce sady Visual Studio vyberte **projektu** > **Application Insights** > **otevřít portál Application Insights**

   ![Snímek obrazovky Přehled služby Application Insights](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> V ukázkovém snímku obrazovky výše **Live Stream**, **doba načtení zobrazení stránky**, a **neúspěšné požadavky** aktuálně nejsou shromažďovány. V další části provede přidání každý. Pokud již shromažďujete **Live Stream**, a **doba načtení zobrazení stránky**, pouze postupujte podle pokynů pro **neúspěšné požadavky**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Shromažďovat neúspěšné požadavky, Live Stream & Doba načtení zobrazení stránky

### <a name="failed-requests"></a>Neúspěšné žádosti

Technicky **neúspěšné požadavky** jsou shromažďovány, ale žádný ještě nedošlo. Chcete urychlit proces podél vlastní výjimky je přidat do existujícího projektu k simulaci reálné výjimky. Pokud vaše aplikace stále běží v sadě Visual Studio před pokračováním **Zastavit ladění** (Shift + F5)

1. V **Průzkumníka řešení** > rozbalte **stránky** > **About.cshtml** > Otevřete **About.cshtml.cs**.

   ![Snímek obrazovky Průzkumníka řešení sady Visual Studio](./media/app-insights-asp-net-core/011-about.png)

2. Přidat výjimku v rámci ``Message=`` a uložte změny do souboru.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>Live Stream

Pro přístup k funkci Live Stream z Application Insights s ASP.NET Core aktualizací **Microsoft.ApplicationInsights.AspNetCore 2.2.0** balíčky NuGet.

Ze sady Visual Studio, vyberte **projektu** > **spravovat balíčky NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > verze **2.2.0** > **aktualizace**.

  ![Snímek obrazovky Správce balíčků NuGet](./media/app-insights-asp-net-core/012-nuget-update.png)

Dojde k několika výzvy k potvrzení. Přečíst a přijmout vyjadřujete souhlas se změnami.

### <a name="page-view-load-time"></a>Doba načtení zobrazení stránky

1. V sadě Visual Studio přejděte do **Průzkumníka řešení** > **stránky** > bude nutné upravit dva soubory: _Layout.cshtml_, a  _ViewImports.cshtml_

2. V __ViewImports.cshtml_, přidejte:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. V **_Layout.cshtml** přidat řádek pod před ``</head>`` značky, ale také před všechny ostatní skripty.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Test se nezdařil, požadavky, doba načtení zobrazení stránky, Live Stream

K otestování a potvrďte, že vše funguje:

1. Spusťte aplikaci klepnutím na službu IIS Express ![Snímek obrazovky sady Visual Studio ikonu IIS Express](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Přejděte **o** stránky k aktivaci výjimku testu. (Pokud spustíte v režimu ladění, budete muset kliknout na **pokračovat** v sadě Visual Studio se zobrazí ve službě Application Insights pro výjimku.)

3. Znovu spustit simulované skriptu transakce PowerShell z předchozí (možná bude potřeba upravit číslo portu ve skriptu.)

4. Pokud aplikace – přehled Insights není stále otevřen v sadě Visual Studio nabídky vyberte možnost **projektu** > **Application Insights** > **otevřete aplikaci Portál insights**. 

   > [!TIP]
   > Pokud ještě nové přenosy nevidíte, zkontrolujte **časový rozsah** a klikněte na tlačítko **aktualizovat**.

   ![Přehled – snímek obrazovky okna](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Vyberte Live Stream

   ![Snímek obrazovky s Live Metrics Stream](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Pokud je stále spuštěný skript Powershellu byste vidět živé metriky, pokud zastavila spuštění skriptu znovu pomocí Live Stream otevřete).

## <a name="app-insights-sdk-comparison"></a>Porovnání App Insights SDK

Intenzivně pracuje produktové skupiny Application Insights k dosažení parity funkcí mezi [úplné rozhraní .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) a.Net Core SDK. 2.2.0 vydání [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) pro Application Insights byl uzavřen do značné míry rozdíly mezi funkcemi.

Chcete-li lépe pochopit, jaké rozdíly a kompromisy mezi [.NET a .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | Porovnání sady SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Živé metriky**      | **+** |**-** | **+** |
   | **Telemetrie kanálu serveru** | **+** |**-** | **+**|
   |**Adaptivní vzorkování**| **+** | **-** | **+**|
   | **Volání závislostí SQL**     | **+** |**-** | **+**|
   | **Čítače výkonu*** | **+** | **-**| **-**|

_Čítače výkonu_ v tomto kontextu označuje [čítače výkonu na straně serveru](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) , jako jsou procesor, paměť a využití disku.

## <a name="open-source-sdk"></a>Open source sad SDK
[Čtení a přispívání ke kódu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Pokračování hledání Application Insights

Abyste lépe pochopili, jak funguje hledání Application Insights v sadě Visual Studio pro projekt ASP.NET Core 2 i v případě, že instalace verze Application Insights NuGet k explicitní ještě neproběhlo balíčky. Může být užitečné si prohlédněte si výstup ladění.

Pokud hledáte výstup pro slovo _insight_ vyzdvihne výsledky podobné následujícím:

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

CoreCLR je načítání dvě sestavení: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

A _Nenakonfigurováno_ v každé instanci Application Insights označuje, že tato aplikace není přidružený k Instrumentační klíč tak, aby se neodesílají do Azure data, která se vygeneruje, když vaše aplikace běží a je pouze telemetrie k dispozici pro místní hledání a analýzu.

Součást jak je to možné je, že balíček NuGet _metabalíček_ přijímá jako závislost [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)

![Snímek obrazovky s NuGet závislost grafu pro metabalíček](./media/app-insights-asp-net-core/013-dependency.png)

Mimo sadu Visual Studio projekt ASP.NET Core v VSCode nebo v nějakém jiném editoru, kterou jste dříve upravovali tato sestavení by zatížení automaticky během ladění Pokud jste nepřidali explicitně Application Insights do projektu.

V sadě Visual Studio, ale tento osvětlení si místní funkce Application Insights z externí sestavení se provádí prostřednictvím použití [IHostingStartup rozhraní](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) která dynamicky přidá Application Insights během ladění.

Další informace o rozšíření aplikace ze [externího sestavení v ASP.NET Core s IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>Jak zakázat Application Insights v projektech Visual Studio .NET Core

Indikátor automatické nahoru funkce hledání Application Insights mohou být užitečné k některým, může být matoucí zajímalo telemetrii ladění, které jsou generovány, pokud jste to neočekávali.

Pokud právě zakazuje generování telemetrie stačí můžete přidat tento blok kódu k metodě konfigurace vaší _Startup.cs_ souboru:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR bude stále načtena _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ a _Microsoft.ApplicationInsights.AspNetCore.dll_, ale nebude provádět žádné kroky.

Pokud chcete úplně vypnout Application Insights v projektu Visual Studio .NET Core, upřednostňovanou metodou je výběr **nástroje** > **možnosti**  >   **Projekty a řešení** > **webové projekty** > a zaškrtněte políčko Zakázat místní Application Insights pro webové projekty ASP.NET Core. Tato funkce přidané ve verzi 15.6 Visual Studio.

![Snímek obrazovky z možnosti okno webových projektů sady Visual Studio obrazovky](./media/app-insights-asp-net-core/014-disable.png)

Pokud používáte starší verzi sady Visual Studio a chcete úplně odebrat všechna sestavení načtená přes IHostingStartup můžete buď přidat:

`.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")`

k _Program.cs_:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                .UseStartup<Startup>()
                .Build();
    }
}
```

Nebo také můžete přidat ``"ASPNETCORE_preventHostingStartup": "True"`` k _launchSettings.json_ proměnné prostředí.

Problém s některou z těchto metod je, že nebude pouze zakáže zakáže nic ve Visual Studiu a které se využití indikátor IHostingStartup nahoru funkce Application Insights.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Další postup
* [Prozkoumejte toky uživatelů](app-insights-usage-flows.md) pochopit, jak uživatelé procházejí vaši aplikaci.
* [Konfigurace shromažďování snímků](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) zobrazíte stav zdrojového kódu a proměnné v tuto chvíli dojde k výjimce.
* [Použití rozhraní API](app-insights-api-custom-events-metrics.md) k odesílání vlastních událostí a metrik pro podrobnější přehled výkonu a využití vaší aplikace.
* [Testy dostupnosti](app-insights-monitor-web-app-availability.md) zkontrolujte vaše aplikace neustále po celém světě.
