---
title: Odebrání Application Insights v aplikaci Visual Studio – Azure Monitor
description: Jak odebrat sadu Application Insights SDK pro ASP.NET a ASP.NET Core v sadě Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 5bfa6ee21cc1a55f653c0e79807a14ac34082e73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90981469"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Jak odebrat Application Insights v aplikaci Visual Studio

V tomto článku se dozvíte, jak odebrat sadu ASP.NET a ASP.NET Core Application Insights SDK v sadě Visual Studio.

Chcete-li odebrat Application Insights, bude nutné odebrat balíčky NuGet a odkazy z rozhraní API ve vaší aplikaci. Balíčky NuGet můžete odinstalovat pomocí konzoly Správa balíčků nebo spravovat řešení NuGet v aplikaci Visual Studio. V následujících částech se zobrazí dva způsoby odebrání balíčků NuGet a to, co bylo automaticky přidáno do projektu. Nezapomeňte potvrdit, že přidané soubory a oblasti ve vlastním kódu, ve kterém jste provedli volání rozhraní API, se odeberou.

## <a name="uninstall-using-the-package-management-console"></a>Odinstalace pomocí konzoly Správa balíčků

# <a name="net"></a>[.NET](#tab/net)

1. Chcete-li otevřít konzolu Správa balíčků, v horní nabídce vyberte nástroje > správce balíčků NuGet > konzola správce balíčků.
     
    ![V horní nabídce klikněte na nástroje > správce balíčků NuGet > konzola správce balíčků.](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Je-li povolena kolekce trasování, je nutné nejprve odinstalovat soubor Microsoft. ApplicationInsights. TraceListener. Zadáním `Uninstall-package Microsoft.ApplicationInsights.TraceListener` následujícího kroku odeberte Microsoft. ApplicationInsights. Web.

1. Zadejte následující příkaz: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    Po zadání příkazu dojde k odinstalaci balíčku Application Insights a všech jeho závislostí z projektu.
    
    ![Zadejte příkaz v konzole.](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Chcete-li otevřít konzolu Správa balíčků, v horní nabídce vyberte nástroje > správce balíčků NuGet > konzola správce balíčků.

    ![V horní nabídce klikněte na nástroje > správce balíčků NuGet > konzola správce balíčků.](./media/remove-application-insights/package-manager.png)

1. Zadejte následující příkaz: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    Po zadání příkazu dojde k odinstalaci balíčku Application Insights a všech jeho závislostí z projektu.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Odinstalace pomocí uživatelského rozhraní NuGet sady Visual Studio

# <a name="net"></a>[.NET](#tab/net)

1. V *Průzkumník řešení*   na pravé straně klikněte pravým tlačítkem na **řešení**   a vyberte **Spravovat balíčky NuGet pro řešení**.

    Zobrazí se obrazovka, která umožňuje upravit všechny balíčky NuGet, které jsou součástí projektu.
    
     ![Klikněte pravým tlačítkem na řešení, v Průzkumník řešení a pak vyberte Spravovat balíčky NuGet pro řešení.](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Pokud je povolené shromažďování trasování, musíte nejdřív odinstalovat Microsoft. ApplicationInsights. TraceListener bez vybraných závislostí a potom podle následujících pokynů odinstalovat Microsoft. ApplicationInsights. Web s vybranými možnostmi odebrat závislosti.
    
1. Klikněte na balíček Microsoft. ApplicationInsights. Web.Na pravé straně zaškrtněte políčko vedle *projektu*   pro výběr všech projektů.
    
1. Chcete-li odebrat všechny závislosti při odinstalaci **Options**, vyberte   rozevírací tlačítko Možnosti pod oddílem, ve kterém jste vybrali možnost projekt.

    V části *Možnosti odinstalace*zaškrtněte políčko u položky *odebrat závislosti*.

1. Vyberte **Odinstalovat**.
    
    ![Snímek obrazovky se zvýrazněným oknem Microsoft. ApplicationInsights. Web a zvýrazněnou možností odebrat závislosti a odinstalování.](./media/remove-application-insights/uninstall-framework.png)

    Zobrazí se dialogové okno, ve kterém se zobrazí všechny závislosti, které mají být z aplikace odebrány.Kliknutím na **tlačítko OK**   odinstalujte.
    
    ![Snímek obrazovky se zobrazí dialogové okno se závislostmi, které mají být odebrány.](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Po odinstalaci všeho se v *Průzkumník řešení*pořád zobrazují "ApplicationInsights.config" a "AiHandleErrorAttribute.cs".Tyto dva soubory můžete odstranit ručně.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. V *Průzkumník řešení*   na pravé straně klikněte pravým tlačítkem na **řešení**   a vyberte **Spravovat balíčky NuGet pro řešení**.

    Zobrazí se obrazovka, která umožňuje upravit všechny balíčky NuGet, které jsou součástí projektu.

    ![Klikněte pravým tlačítkem na řešení, v Průzkumník řešení a pak vyberte Spravovat balíčky NuGet pro řešení.](./media/remove-application-insights/manage-nuget-core.png)

1. Klikněte na balíček Microsoft. ApplicationInsights. AspNetCore. Na pravé straně zaškrtněte políčko vedle pole *projekt* pro výběr všech projektů a potom vyberte možnost **odinstalovat**.

    ![Zaškrtněte odebrat závislosti a potom odinstalujte.](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Co se vytvoří při přidávání Application Insights

Když do projektu přidáte Application Insights, vytvoří soubory a přidá kód do některých z vašich souborů. Pouze odinstalování balíčků NuGet nebude soubory a kód vždy zahozena. Chcete-li zcela odebrat Application Insights, měli byste pomocí všech volání rozhraní API, které jste přidali do projektu, ručně odstranit přidaný kód nebo soubory.

# <a name="net"></a>[.NET](#tab/net)

Když přidáte Telemetrie Application Insights do projektu aplikace Visual Studio ASP.NET, přidá následující soubory:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

Jsou přidány následující části kódu:

- [Název vašeho projektu]. csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout. cshtml

    Pokud má váš projekt soubor layout. cshtml, je přidán kód níže.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.jsna

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Když přidáte Telemetrie Application Insights do projektu šablony ASP.NET Core sady Visual Studio, přidá následující kód:

- [Název vašeho projektu]. csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.js:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.jsna
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Další kroky

- [Azure Monitor](../overview.md)
