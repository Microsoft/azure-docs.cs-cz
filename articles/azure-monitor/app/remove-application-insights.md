---
title: Odebrání přehledů aplikací ve Visual Studiu – Azure Monitor
description: Jak odebrat application insights SDK pro ASP.NET a ASP.NET core v sadě Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80805102"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Odebrání přehledů aplikací v sadě Visual Studio

Tento článek vám ukáže, jak odebrat ASP.NET a ASP.NET základní aplikace Insights SDK v sadě Visual Studio.

Chcete-li odebrat Application Insights, budete muset odebrat balíčky NuGet a odkazy z rozhraní API ve vaší aplikaci. Balíčky NuGet můžete odinstalovat pomocí Konzoly pro správu balíčků nebo Spravovat řešení NuGet v sadě Visual Studio. V následujících částech se zobrazí dva způsoby, jak odebrat balíčky NuGet a co bylo automaticky přidáno do projektu. Nezapomeňte potvrdit přidané soubory a oblasti s vlastním kódem, ve kterém jste volali do rozhraní API, jsou odebrány.

## <a name="uninstall-using-the-package-management-console"></a>Odinstalace pomocí Konzoly pro správu balíčků

# <a name="net"></a>[.NET](#tab/net)

1. Chcete-li otevřít Konzolu pro správu balíčků, vyberte v horní nabídce možnost Nástroje > Správce balíčků NuGet > konzoli správce balíčků.
     
    ![V horní nabídce klepněte na položku Nástroje > Správce balíčků > konzoli Správce balíčků](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Pokud je povolena kolekce trasování, musíte nejprve odinstalovat Microsoft.ApplicationInsights.TraceListener. Zadejte `Uninstall-package Microsoft.ApplicationInsights.TraceListener` následující krok a odeberte soubor Microsoft.ApplicationInsights.Web.

1. Zadejte následující příkaz: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    Po zadání příkazu bude balíček Application Insights a všechny jeho závislosti odinstalovány z projektu.
    
    ![Příkaz Enter v konzoli](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Chcete-li otevřít Konzolu pro správu balíčků, vyberte v horní nabídce možnost Nástroje > Správce balíčků NuGet > konzoli správce balíčků.

    ![V horní nabídce klepněte na položku Nástroje > Správce balíčků > konzoli Správce balíčků](./media/remove-application-insights/package-manager.png)

1. Zadejte následující příkaz: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    Po zadání příkazu bude balíček Application Insights a všechny jeho závislosti odinstalovány z projektu.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Odinstalace pomocí uzly Visual Studio NuGet

# <a name="net"></a>[.NET](#tab/net)

1. V *Průzkumníku* řešení vpravo klikněte pravým **tlačítkem** myši na řešení a vyberte **spravovat balíčky NuGet pro řešení**.

    Potom se zobrazí obrazovka, která umožňuje upravit všechny balíčky NuGet, které jsou součástí projektu.
    
     ![Klikněte pravým tlačítkem myši na Řešení v Průzkumníku řešení a potom vyberte Spravovat balíčky NuGet pro řešení.](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Pokud je povolena kolekce trasování, je třeba nejprve odinstalovat Microsoft.ApplicationInsights.TraceListener bez zaškrtnuté odebrat závislosti a potom postupujte podle následujících kroků odinstalovat Microsoft.ApplicationInsights.Web s odebrat závislosti vybrané.
    
1. Klikněte na balíček Microsoft.ApplicationInsights.Web.Na pravé straně zaškrtněte políčko vedle *aplikace Project a* vyberte všechny projekty.
    
1. Chcete-li při odinstalaci odebrat všechny závislosti, vyberte rozbalovací tlačítko **Možnosti** pod oddílem, ve kterém jste vybrali projekt.

    V části *Možnosti odinstalace*zaškrtněte políčko *vedle položky Odebrat závislosti*.

1. Vyberte **možnost Odinstalovat**.
    
    ![Kontrola odebrání závislostí a odinstalace](./media/remove-application-insights/uninstall-framework.png)

    Zobrazí se dialogové okno, které zobrazuje všechny závislosti, které mají být odebrány z aplikace.Chcete-li odinstalovat, vyberte **možnost OK.** 
    
    ![Kontrola odebrání závislostí a odinstalace](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Po odinstalaci se může stále zobrazit "ApplicationInsights.config" a "AiHandleErrorAttribute.cs" v *Průzkumníku řešení*.Oba soubory můžete odstranit ručně.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. V *Průzkumníku* řešení vpravo klikněte pravým **tlačítkem** myši na řešení a vyberte **spravovat balíčky NuGet pro řešení**.

    Potom se zobrazí obrazovka, která umožňuje upravit všechny balíčky NuGet, které jsou součástí projektu.

    ![Klikněte pravým tlačítkem myši na Řešení v Průzkumníku řešení a potom vyberte Spravovat balíčky NuGet pro řešení.](./media/remove-application-insights/manage-nuget-core.png)

1. Klikněte na balíček Microsoft.ApplicationInsights.AspNetCore. Na pravé straně zaškrtněte políčko vedle *aplikace Project* a vyberte všechny projekty a vyberte **odinstalovat**.

    ![Kontrola odebrání závislostí a odinstalace](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Co se vytvoří při přidání přehledů aplikací

Když do projektu přidáte Application Insights, vytvoří se soubory a do některých souborů přidá kód. Pouze odinstalování NuGet balíčky nebude vždy zahodit soubory a kód. Chcete-li plně odebrat Application Insights, měli byste zkontrolovat a ručně odstranit přidaný kód nebo soubory spolu s voláními rozhraní API, která jste přidali do projektu.

# <a name="net"></a>[.NET](#tab/net)

Když přidáte telemetrickou vlastnosti Application Insights do projektu ASP.NET sady Visual Studio, přidá se následující soubory:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

Doplňují se nové části kódu:

- [Název vašeho projektu].csproj

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

- Layout.cshtml

    Pokud má váš projekt soubor Layout.cshtml, přidá se následující kód.
    
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

- ConnectedService.json

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

Když přidáte telemetrii Application Insights do projektu šablony Visual Studio ASP.NET Core, přidá se následující kód:

- [Název vašeho projektu].csproj

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

- Appsettings.json:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
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

- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)