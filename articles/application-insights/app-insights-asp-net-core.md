---
title: Azure Application Insights pro ASP.NET Core | Microsoft Docs
description: Sledování webových aplikací pro dostupnosti, výkonu a využití.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 459fbbe734577af474fae8de3acf9be732780d6c
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736530"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights pro ASP.NET Core

Azure Application Insights poskytuje hlubší monitorování webové aplikace můžete snížit úroveň kódu. Můžete snadno monitorování vaší webové aplikace pro dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé.

Tento článek vás provede procesem vytvoření ukázkové ASP.NET Core [stránky Razor](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) aplikace v sadě Visual Studio a jak začít monitorovat pomocí služby Azure Application Insights.

## <a name="prerequisites"></a>Požadavky

- NET Core 2.0.0 SDK nebo novější.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) verze 15.7.3 nebo novějším s ASP.NET a webové úlohy vývoj. 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Vytvoření projektu ASP.NET Core v sadě Visual Studio

1. Klikněte pravým tlačítkem myši a spusťte **Visual Studio 2017** jako správce.
2. Vyberte **soubor** > **nové** > **projektu** (Ctrl-Shift-N).

   ![Snímek obrazovky nabídky Nový projekt sady Visual Studio souboru](./media/app-insights-asp-net-core/001-new-project.png)

3. Rozbalte položku **Visual C#** > vyberte **.NET Core** > **webové aplikace ASP.NET Core**. Zadejte **název** > **název řešení** > zkontrolujte **vytvoření nového úložiště Git**.

   ![Snímek obrazovky Průvodce projektem nový soubor sady Visual Studio](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Vyberte **.Net Core** > **jádro ASP.NET 2.0** **webovou aplikaci** > **OK**.

    ![Snímek obrazovky nabídky Nový projekt výběr sady Visual Studio souboru](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Hledání Application Insights

Ve výchozím nastavení v sadě Visual Studio 2015 Update 2 nebo novější s ASP.NET Core 2 + na základě projektu je verze můžete využít výhod [hledání Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-visual-studio) i před explicitně přidáte Application Insights do projektu.

K otestování této funkce:

1. Spuštění aplikace kliknutím IIS Express ![Snímek obrazovky nástroje Visual Studio ikonu IIS Express](./media/app-insights-asp-net-core/004-iis-express.png)

2. Vyberte **zobrazení** > **ostatní okna** > **hledání Application Insights**.

   ![Snímek obrazovky diagnostické nástroje sady Visual Studio](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. Telemetrie relace ladění je aktuálně k dispozici pro pouze místní analýzu. Chcete-li plně Application Insights, vyberte **Telemetrie připravenosti** v horní pravým nebo postupujte podle kroků v další části.

   ![Snímek obrazovky Visual Studio Application Insights vyhledávání](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Další informace o si indikátory Visual Studio si funkce jako [hledání Application Insights](app-insights-visual-studio.md) a [Codelensu](app-insights-visual-studio-codelens.md) místně předtím, než přidáte Application Insights do projektu ASP.NET Core se podívejte se Vysvětlení v [konci tohoto článku.](#Application-Insights-search-continued)

## <a name="add-application-insights-telemetry"></a>Přidání Telemetrie Application Insights

1. Vyberte **projektu** > **přidat Telemetrii Application Insights...** . (Nebo můžete kliknout pravým tlačítkem **připojené služby** a vyberte možnost Přidat připojení služby.)

    ![Snímek obrazovky nabídky Nový projekt výběr sady Visual Studio souboru](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Vyberte **Začínáme**. (V závislosti na vaší verzi sady Visual Studio text může mírně lišit. Místo toho mají některé starší verze **spustit volné** tlačítko.)

    ![Snímek obrazovky nabídky Nový projekt výběr sady Visual Studio souboru](./media/app-insights-asp-net-core/008-get-started.png)

3. Vyberte odpovídající **předplatné** > **prostředků** > **zaregistrovat**.

## <a name="changes-made-to-your-project"></a>Změní Made do projektu

Application Insights je nízkou režii. Chcete-li zkontrolovat na všechny změny do projektu přidáním telemetrie Application Insights:

Vyberte **zobrazení** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projektu** > **změny**

- Celkový počet čtyři změny:

  ![Snímek obrazovky soubory změnit tak, že přidáte Application Insights](./media/app-insights-asp-net-core/009-changes.png)

- K vytvoření nového souboru jeden:

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

## <a name="synthetic-transactions-with-powershell"></a>Syntetických transakcí v prostředí PowerShell

K automatizaci požadavky DHCP proti vaší aplikace pomocí syntetické transakce.

1. Spuštění aplikace kliknutím IIS Express ![Snímek obrazovky nástroje Visual Studio ikonu IIS Express](./media/app-insights-asp-net-core/004-iis-express.png)

2. Zkopírujte adresu url z panelu Adresa prohlížeče. Je ve formátu http://localhost:{random číslo portu}

   ![Snímek obrazovky panelu Adresa v prohlížeči adresu url](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Spusťte následující zacyklení prostředí PowerShell k vytvoření 100 syntetické transakce proti testování aplikace. Změňte číslo portu, po **localhost:** tak, aby odpovídaly adresu url, kterou jste zkopírovali v předchozím kroku.

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Otevřete aplikaci portálu Statistika

Po spuštění PowerShell z předchozí části, spusťte Application Insights se mají zobrazit transakce a potvrďte, že data jsou shromažďována. 

V nabídce sady Visual Studio vyberte **projektu** > **Application Insights** > **otevřete portálu Statistika aplikace**

   ![Snímek obrazovky Statistika aplikací – přehled](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> Ve výše uvedený snímek obrazovky příklad **živý datový proud**, **zobrazení času načítání stránky**, a **neúspěšné požadavky** nejsou aktuálně shromažďovat. V další části provede přidání každý. Pokud již shromažďujete **živý datový proud**, a **zobrazení času načítání stránky**, pouze postupujte podle pokynů pro **neúspěšné požadavky**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Shromažďovat neúspěšných požadavků, živý datový proud & čas načítání stránky zobrazení

### <a name="failed-requests"></a>Neúspěšné žádosti

Technicky **neúspěšné požadavky** jsou shromažďovány, ale zatím žádná došlo. V zájmu urychlení proces podél vlastní výjimky lze přidat do existujícího projektu pro simulaci reálného výjimky. Pokud vaše aplikace stále běží v sadě Visual Studio před pokračováním **Zastavte ladění** (Shift + F5)

1. V **Průzkumníku řešení** > rozbalte **stránky** > **About.cshtml** > Otevřete **About.cshtml.cs**.

   ![Snímek obrazovky Průzkumníka řešení Visual Studio](./media/app-insights-asp-net-core/011-about.png)

2. Přidat výjimku pod ``Message=`` a uložte změny do souboru.

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

Pro přístup k funkci živý datový proud Application Insights s ASP.NET Core aktualizace **Microsoft.ApplicationInsights.AspNetCore 2.2.0** balíčky NuGet.

Ze sady Visual Studio, vyberte **projektu** > **spravovat balíčky NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > verze **2.2.0** > **aktualizace**.

  ![Snímek obrazovky Správce balíčků NuGet](./media/app-insights-asp-net-core/012-nuget-update.png)

Dojde k více výzev k potvrzení. Přečtěte si a přijměte, pokud souhlasíte se změnami.

### <a name="page-view-load-time"></a>Čas načítání stránky zobrazení

1. V sadě Visual Studio přejděte do **Průzkumníku řešení** > **stránky** > bude nutné upravit dva soubory: _Layout.cshtml_, a  _ViewImports.cshtml_

2. V __ViewImports.cshtml_, přidejte:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. V **_Layout.cshtml** přidejte řádek níže před ``</head>`` značky, ale i před jiné skripty.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Test se nezdařil, požadavků, čas načítání stránky zobrazení, živý datový proud

K otestování a ověřte, zda vše funguje:

1. Spuštění aplikace kliknutím IIS Express ![Snímek obrazovky nástroje Visual Studio ikonu IIS Express](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Přejděte na **o** stránky k aktivaci testovací výjimka. (Pokud používáte v režimu ladění, budete muset klikněte na tlačítko **pokračovat** v sadě Visual Studio pro výjimky objeví ve službě Application Insights.)

3. Znovu spustit Simulovaná skript prostředí PowerShell transakce z dříve (možná muset upravit číslo portu ve skriptu.)

4. Pokud není stále otevřít z nabídky vyberte možnost Visual Studio přehled Statistika aplikací **projektu** > **Application Insights** > **otevřete aplikace Portálu Statistika**. 

   > [!TIP]
   > Pokud nevidíte nový provozu ještě, podívejte se **čas rozsah** a klikněte na tlačítko **aktualizovat**.

   ![Snímek obrazovky Přehled okna](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Vyberte živý datový proud

   ![Snímek obrazovky za provozu metriky datového proudu](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   Pokud vaše prostředí PowerShell skriptu je stále spuštěná jste měli vidět za provozu metriky, pokud zastavil spustit skriptu znovu pomocí živý datový proud otevřete (.)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK porovnání

Pevného pracuje se product group Application Insights k dosažení parity funkcí mezi [úplné rozhraní .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) a .net Core SDK. 2.2.0 vydání [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) pro Application Insights do značné míry zavřel funkce mezery.

Další informace o rozdílech a kompromisy mezi [.NET a .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | Porovnání SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Metriky za provozu**      | **+** |**-** | **+** |
   | **Kanálu Telemetrii serveru** | **+** |**-** | **+**|
   |**Adaptivního vzorkování**| **+** | **-** | **+**|
   | **Volání závislost SQL**     | **+** |**-** | **+**|
   | **Čítače výkonu*** | **+** | **-**| **-**|

_Čítače výkonu_ v tomto kontextu odkazuje na [čítače výkonu na straně serveru](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) jako procesor, paměť a využití disku.

## <a name="open-source-sdk"></a>Open-source SDK
[Přečtěte si a přispívat ke kódu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Hledání Application Insights dál

Abyste lépe pochopili, jak funguje hledání Application Insights v sadě Visual Studio pro projekt ASP.NET Core 2 i v případě, že explicitní instalace balíčku nuget Statistika aplikace nebyly ještě neproběhlo balíčky. Může být užitečné k vyhledejte ve výstupu ladění.

Pokud hledáte výstup slovo _přehledy_ zvýrazní výsledky podobné následujícím:

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

CoreCLR je načítání dvě sestavení: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

A _nenakonfigurován_ v každé instanci Application Insights telemetrie označuje, že tato aplikace není přidružený ikey tak, aby se neodesílají do Azure data, která se vygeneruje, když aplikace běží a je pouze k dispozici pro místní hledání a analýzu.

Součást jak je možné je, že balíček NuGet _Microsoft.AspNetCore.All_ přebírá jako závislost [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)

![Snímek obrazovky NuGet závislostí graf pro Microsoft.AspNETCore.all](./media/app-insights-asp-net-core/013-dependency.png)

Mimo Visual Studio Pokud byly změny projektu ASP.NET Core VSCode nebo některých jiných editoru tyto sestavení by načíst automaticky během ladění Pokud nepřidali jste explicitně Application Insights do projektu.

V sadě Visual Studio však tato osvětlení si místní funkcí Application Insights z externí sestavení se provádí prostřednictvím použití [IHostingStartup rozhraní](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) dynamicky přidává Application Insights během ladění.

Další informace o rozšíření aplikace z [externí sestavení v ASP.NET Core s IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>Zakázání Application Insights v projektech Visual Studio .NET Core

Automatické světlým až funkcí Application Insights hledání mohou být užitečné k některým, může to být vidět telemetrie ladění, které jsou generovány, pokud jste ji neočekávali matoucí.

Pokud je právě zakázání generování telemetrie dostatečná můžete přidat tento blok kódu k metodě konfigurace vaší _Startup.cs_ souboru:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

Stále CoreCLR načte _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ a _Microsoft.ApplicationInsights.AspNetCore.dll_, ale nebude provádět žádné kroky.

Pokud chcete úplně vypnout Application Insights ve vašem projektu Visual Studio .NET Core, upřednostňovanou metodou je výběr **nástroje** > **možnosti**  >   **Projekty a řešení** > **webové projekty** > a zaškrtněte políčko Zakázat místní Application Insights pro webové projekty ASP.NET Core. Tato funkce se přidal ve Visual Studio 15,6 operací.

![Snímek obrazovky z možnosti okno webových projektů sady Visual Studio obrazovky](./media/app-insights-asp-net-core/014-disable.png)

Pokud používáte starší verze sady Visual Studio a chcete úplně odebrat všechny sestavení načíst prostřednictvím IHostingStartup můžete buď přidat:

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

Nebo můžete také můžete přidat ``"ASPNETCORE_preventHostingStartup": "True"`` k _launchSettings.json_ proměnné prostředí.

Problém s některým z těchto postupů je, že ji nebude pouze zakáže Application Insights nic v sadě Visual Studio, který byl využití světlým IHostingStartup si funkce vypne.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Další postup
* [Prozkoumejte toků uživatelé](app-insights-usage-flows.md) pochopit, jak uživatelé přecházejí mezi vaší aplikace.
* [Konfigurace shromažďování snímků](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) zobrazíte stav zdrojového kódu a proměnných v tuto chvíli je vyvolána výjimka.
* [Použít rozhraní API](app-insights-api-custom-events-metrics.md) k odeslání vlastní události a metriky pro další podrobné zobrazení výkonu a využití vaší aplikace.
* [Testy dostupnosti](app-insights-monitor-web-app-availability.md) zkontrolujte aplikaci neustále z po celém světě.
