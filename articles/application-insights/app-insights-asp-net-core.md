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
ms.openlocfilehash: 3e838cd45eefa5b5b644992bb8eae36abf3ec33a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276342"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights pro ASP.NET Core

Azure Application Insights poskytuje podrobné monitorování webové aplikace na úrovni kódu. Můžete snadno monitorovat webové aplikace z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé.

Tento článek vás provede kroky k vytvoření vzorku ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) aplikace v sadě Visual Studio. Je také ukazuje, jak začít monitorovat pomocí Application Insights.

## <a name="prerequisites"></a>Požadavky

- .NET core 2.0.0 SDK nebo novější
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) verze 15.7.3 nebo novější, s úlohou vývoj pro ASP.NET a web

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Vytvoření projektu aplikace ASP.NET Core v sadě Visual Studio

1. Klikněte pravým tlačítkem na **Visual Studio 2017**a pak vyberte **spustit jako správce**.
2. Vyberte **souboru** > **nové** > **projektu** (Ctrl + Shift + N).

   ![Snímek obrazovky sady Visual Studio nový projekt](./media/app-insights-asp-net-core/001-new-project.png)

3. Rozbalte **Visual C#**. Vyberte **.NET Core** > **webová aplikace ASP.NET Core**. Zadejte název projektu a název řešení a pak vyberte **vytvořit nové úložiště Git**.

   ![Průvodce novým projektem snímek obrazovky sady Visual Studio](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Vyberte **.NET Core** > **ASP.NET Core 2.0** **webovou aplikaci** > **OK**.

    ![Výběr šablony projektu nový snímek obrazovky sady Visual Studio](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Hledání Application Insights

V aplikaci Visual Studio 2015 Update 2 nebo novější s projektu aplikace ASP.NET Core 2 + na základě můžete využít výhod [hledání Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio), ještě než explicitně přidat Application Insights do projektu.

K otestování této funkce:

1. Spuštění aplikace. Ke spuštění vaší aplikace, vyberte **služby IIS Express** ikonu (![snímek obrazovky sady Visual Studio služby IIS Express ikonu](./media/app-insights-asp-net-core/004-iis-express.png)).

2. Vyberte **zobrazení** > **jiných Windows** > **hledání Application Insights**.

   ![Snímek obrazovky sady Visual Studio diagnostické nástroje Výběr](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. V současné době je dostupná jenom pro místní analýzu telemetrii relace ladění. Chcete-li plně povolte Application Insights, vyberte **připravenost Telemetrie** v horním pravém rohu, nebo úplné kroků uvedených v následující části.

   ![Snímek obrazovky sady Visual Studio Application Insights hledání](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Další informace o jak Visual Studio světla do funkce jako [hledání Application Insights](app-insights-visual-studio.md) a [CodeLens](app-insights-visual-studio-codelens.md) místně předtím, než přidáte Application Insights do projektu ASP.NET Core, najdete v článku [ Hledání Application Insights pokračování](#application-insights-search-continued).

## <a name="add-application-insights-telemetry"></a>Přidání Telemetrie Application Insights

1. Vyberte **projektu** > **přidat Telemetrii Application Insights**. (Nebo můžete kliknout pravým tlačítkem **připojené služby**a pak vyberte **přidat připojenou službu**.)

    ![Snímek obrazovky sady Visual Studio nový výběr projektu](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Vyberte **Začínáme**. (V závislosti na vaší verzi sady Visual Studio, text se mohou mírně lišit. Mají některé starší verze **začít zdarma** tlačítko místo.)

    ![Snímek obrazovky z Application Insights Začínáme tlačítko](./media/app-insights-asp-net-core/008-get-started.png)

3. Vyberte své předplatné a pak vyberte **prostředků** > **zaregistrovat**.

## <a name="changes-made-to-your-project"></a>Změny provedené v projektu

Application Insights je nízkou režii. Zkontrolovat změny do svého projektu tak, že přidáte telemetrii Application Insights:

Vyberte **zobrazení** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projektu** > **změny**

- Zobrazí se čtyři celkový počet změn:

  ![Snímek obrazovky se soubory změnit tak, že přidáte Application Insights](./media/app-insights-asp-net-core/009-changes.png)

- Vytvoření jednoho souboru:

  -  _ConnectedService.json_

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "8.12.10405.1",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```

- Tři soubory jsou změny (Další poznámky přidané zvýrazněte změny):

  - _appSettings.JSON_:

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

  - _ContosoDotNetCore.csproj_:

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

  -  _Soubor program.cs_:

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

K automatizaci požadavky na vaši aplikaci za použití syntetických transakcí:

1. Ke spuštění vaší aplikace, vyberte ![Snímek obrazovky sady Visual Studio ikonu IIS Express](./media/app-insights-asp-net-core/004-iis-express.png) ikona.

2. Zkopírujte adresu URL z adresního řádku prohlížeče. Adresa URL je ve formátu `http://localhost:<port number>`.

   ![Snímek obrazovky s prohlížeči adresu URL do adresního řádku](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Spusťte následující smyčka prostředí PowerShell pro vytvoření 100 syntetické transakce pomocí aplikace pro vaše testy. Změňte číslo portu po `localhost:` tak, aby odpovídaly adresu URl, kterou jste zkopírovali v předchozím kroku. Příklad:

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-the-application-insights-portal"></a>Otevřít portál Application Insights

Po spuštění příkazů prostředí PowerShell v předchozí části, otevřete službu Application Insights, chcete-li zobrazit transakce a potvrďte, že se shromažďují data. 

V nabídce sady Visual Studio vyberte **projektu** > **Application Insights** > **otevřít portál Application Insights**.

   ![Snímek obrazovky Přehled služby Application Insights](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> V předchozím ukázkovém snímku obrazovky **Live Stream**, **doba načtení zobrazení stránky**, a **neúspěšné požadavky** nejsou shromažďovány. Následující části vás provede kroky pro přidání každý z nich. Pokud již shromažďujete **Live Stream** a **doba načtení zobrazení stránky**, pouze pro, proveďte kroky **neúspěšné požadavky**.

## <a name="collect-failed-requests-live-stream-and-page-view-load-time"></a>Shromažďovat neúspěšné požadavky, Live Stream a doba načtení zobrazení stránky

### <a name="failed-requests"></a>Neúspěšné žádosti

Technicky je shromažďováno neúspěšné požadavky, ale žádné neúspěšné žádosti ještě nedošlo. Pokud chcete urychlit proces, můžete přidat vlastní výjimky do existujícího projektu k simulaci reálné výjimky. Pokud vaše aplikace stále běží v sadě Visual Studio, než budete pokračovat, vyberte **Zastavit ladění** (Shift + F5).

1. V **Průzkumníka řešení**, rozbalte **stránky** > **About.cshtml**a pak otevřete *About.cshtml.cs*.

   ![Snímek obrazovky Průzkumníka řešení sady Visual Studio](./media/app-insights-asp-net-core/011-about.png)

2. Přidat výjimku v rámci ``Message=``a potom uložte změny do souboru.

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

Pro přístup k funkci Live Stream z Application Insights s ASP.NET Core, aktualizujte balíčky NuGet Microsoft.ApplicationInsights.AspNetCore 2.2.0.

V sadě Visual Studio, vyberte **projektu** > **spravovat balíčky NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > (verze) **2.2.0** > **aktualizace**.

  ![Snímek obrazovky Správce balíčků NuGet](./media/app-insights-asp-net-core/012-nuget-update.png)

Zobrazí se více výzvy k potvrzení. Přečíst a přijmout vyjadřujete souhlas se změnami.

### <a name="page-view-load-time"></a>Doba načtení zobrazení stránky

1. V sadě Visual Studio, přejděte na **Průzkumníka řešení** > **stránky**. Je třeba upravit dva soubory: *Layout.cshtml* a *ViewImports.cshtml*.

2. V *ViewImports.cshtml*, přidejte tento kód:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. V *Layout.cshtml*, přidejte následující kód před ``</head>`` značky a před všechny ostatní skripty:

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-and-live-stream"></a>Neúspěšné požadavky, doba načtení zobrazení stránky, testování a Live Stream

Pro testování a potvrďte, že vše funguje:

1. Spuštění aplikace. Ke spuštění vaší aplikace, vyberte ![Snímek obrazovky sady Visual Studio ikonu IIS Express](./media/app-insights-asp-net-core/004-iis-express.png) ikona.

2. Přejděte **o** stránky k aktivaci výjimku testu. (Pokud jste v režimu ladění v sadě Visual Studio, vyberte **pokračovat** výjimky objeví ve službě Application Insights.)

3. Znovu spusťte simulované skriptu transakce PowerShell, který jste použili dříve. (Může být potřeba upravit číslo portu ve skriptu.)

4. Pokud **přehled** stránce v aplikacích Insights není stále otevřen v nabídce sady Visual Studio, vyberte **projektu** > **Application Insights**  >  **Otevřít portál Application Insights**. 

   > [!TIP]
   > Pokud nevidíte nové přenosy dat, zkontrolujte hodnotu **časový rozsah**a pak vyberte **aktualizovat**.

   ![Snímek obrazovky okna přehledu](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Vyberte **Live Stream**.

   ![Snímek obrazovky s Live Metrics Stream](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Pokud váš skript prostředí PowerShell je stále spuštěna, měli byste vidět živé metriky. Pokud váš skript prostředí PowerShell byla zastavena, spusťte znovu skript pro otevřete Live Stream.)

## <a name="application-insights-sdk-comparison"></a>Porovnání Application Insights SDK

Intenzivně pracuje produktové skupiny Application Insights k dosažení parity funkcí mezi [úplné rozhraní .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) a .NET Core SDK. 2.2.0 vydání [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) pro Application Insights uzavírá velké rozdíly mezi funkcemi.

Následující tabulka popisuje další rozdíly a kompromisy mezi [.NET a .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server):

   | Porovnání sady SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Živé metriky**      | **+** |**-** | **+** |
   | **Telemetrie kanálu serveru** | **+** |**-** | **+**|
   |**Adaptivní vzorkování**| **+** | **-** | **+**|
   | **Volání závislostí SQL**     | **+** |**-** | **+**|
   | **Čítače výkonu*** | **+** | **-**| **-**|

Čítače výkonu v tomto kontextu najdete [čítače výkonu na straně serveru](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) , jako jsou procesor, paměť a využití disku.

## <a name="open-source-sdk"></a>Open source sad SDK
[Číst a přidávat do kódu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="application-insights-search-continued"></a>Pokračovat v hledání Application Insights

Tato část vám umožňují lépe pochopit, jak funguje hledání Application Insights v sadě Visual Studio pro projekt ASP.NET Core 2. Tento způsob, jak to funguje i v případě, že jste si nenainstalovali explicitně balíčky Application Insights NuGet ještě. Může být také užitečné si prohlédněte si výstup ladění.

Pokud hledáte výstup pro slovo _insight_, jsou zvýrazněny výsledky podobné následujícím:

```DebugOutput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

Ve výstupu načte CoreCLR dvě sestavení: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

_Nenakonfigurováno_ odkaz v každé instanci telemetrii Application Insights označuje, že tato aplikace není přidružený k Instrumentační klíč. Data, která se vygeneruje, když vaše aplikace běží neposílají se do Azure. Data jsou k dispozici pouze pro místní hledání a analýzu.

Funkce je možné, v části protože balíček NuGet _metabalíček_ trvá [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_ ](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1) jako závislost.

![Snímek obrazovky s NuGet graf závislostí pro metabalíček](./media/app-insights-asp-net-core/013-dependency.png)

Mimo sadu Visual Studio kterou jste dříve upravovali projekt ASP.NET Core v VSCode nebo jiném editoru, tato sestavení by zatížení automaticky během ladění Pokud jste nepřidali explicitně Application Insights do projektu.

V sadě Visual Studio, ale tento osvětlení si místní funkce Application Insights z externí sestavení se provádí pomocí [IHostingStartup rozhraní](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1). Rozhraní dynamicky přidá Application Insights během ladění.

Další informace o rozšíření aplikace ze [externího sestavení v ASP.NET Core s IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="disable-application-insights-in-visual-studio-net-core-projects"></a>Zakázat Application Insights v projektech Visual Studio .NET Core

I když hledání automatické nahoru světla služby Application Insights zobrazuje telemetrii ladění vygeneruje, když jste neočekávali, že je možné, že dojde k záměně mohou být užitečné, funkce.

Pokud právě zakazuje generování telemetrie je dostačující, můžete přidat tento blok kódu do **nakonfigurovat** metodu vaše _Startup.cs_ souboru:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR je stále načítá _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ a _Microsoft.ApplicationInsights.AspNetCore.dll_, ale nic nedělají soubory.

Pokud chcete úplně vypnout Application Insights v projektu Visual Studio .NET Core, upřednostňovanou metodou je výběr **nástroje** > **možnosti**  >   **Projekty a řešení** > **webových projektů**. Vyberte **zakázat místní Application Insights pro webové projekty ASP.NET Core** zaškrtávací políčko. Tato funkce přidané ve verzi 15.6 Visual Studio.

![Snímek obrazovky z možnosti okno webových projektů sady Visual Studio obrazovky](./media/app-insights-asp-net-core/014-disable.png)

Pokud používáte starší verzi sady Visual Studio a chcete úplně odebrat všechna sestavení, které byly načteny prostřednictvím *IHostingStartup*, máte dvě možnosti:

* Přidat `.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")` k _Program.cs_:

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

* Přidat ``"ASPNETCORE_preventHostingStartup": "True"`` k _launchSettings.json_ proměnné prostředí.

Problém s některou z těchto metod je, že se nemusíte zakážou pouze služba Application Insights. Nic v sadě Visual Studio, který používal metody také zakázat *IHostingStartup* funkce světla nahoru.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Další postup
* [Prozkoumejte toky uživatelů](app-insights-usage-flows.md) pochopit, jak uživatelé procházejí vaši aplikaci.
* [Konfigurace shromažďování snímků](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) zobrazíte stav zdrojového kódu a proměnné v tuto chvíli dojde k výjimce.
* [Použití rozhraní API](app-insights-api-custom-events-metrics.md) k odesílání vlastních událostí a metrik pro podrobnější přehled výkonu a využití vaší aplikace.
* Použití [testy dostupnosti](app-insights-monitor-web-app-availability.md) ke kontrole neustále z aplikace po celém světě.
