---
title: Diagnostikovat selhání a výjimky pomocí Azure Application Insights
description: Zachyťte výjimky z ASP.NET aplikací spolu s telemetrií požadavků.
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: ccfcb354e27d36f40810b114a1729cf6addf8fb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294695"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnostika výjimky ve webových aplikacích pomocí služby Application Insights
Výjimky ve vaší živé webové aplikaci hlásí [Application Insights](../../azure-monitor/app/app-insights-overview.md). Můžete korelovat neúspěšné požadavky s výjimkami a dalšími událostmi na straně klienta i serveru, takže můžete rychle diagnostikovat příčiny.

## <a name="set-up-exception-reporting"></a>Nastavit hlášení výjimek
* Chcete-li mít z serverové aplikace nahlášené výjimky:
  * Webové aplikace Azure: Přidání [rozšíření Application Insights](../../azure-monitor/app/azure-web-apps.md)
  * Škálovací sady azure virtuálních počítačů a škálovací sada virtuálních strojů Azure hostované v aplikacích hostovaných službou IIS: Přidání [rozšíření pro monitorování aplikací](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * Nainstalujte do kódu aplikace sady [Application Insights SDK](../../azure-monitor/app/asp-net.md) nebo
  * Webové servery služby IIS: Spustit [agenta Application Insights](../../azure-monitor/app/monitor-performance-live-website-now.md); Nebo
  * Webové aplikace java: Instalace [agenta Java](../../azure-monitor/app/java-agent.md)
* Nainstalujte [fragment JavaScriptu](../../azure-monitor/app/javascript.md) do svých webových stránek, abyste zachytili výjimky prohlížeče.
* V některých aplikačních architekturách nebo s některými nastaveními je třeba provést některé další kroky k zachycení dalších výjimek:
  * [Webové formuláře](#web-forms)
  * [MVC](#mvc)
  * [Webové rozhraní API 1.*](#web-api-1x)
  * [Webové rozhraní API 2.*](#web-api-2x)
  * [WCF](#wcf)

  Tento článek je specificky zaměřena na aplikace rozhraní .NET Framework z hlediska příkladu kódu. Některé metody, které fungují pro rozhraní .NET Framework, jsou v ktě .NET Core SDK zastaralé. Pokud máte aplikaci .NET Core, přečtěte si dokumentaci k [k sada .NET Core SDK.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnostika výjimek pomocí sady Visual Studio
Otevřete řešení aplikace v Sadě Visual Studio, které vám pomůže s laděním.

Spusťte aplikaci, a to buď na serveru nebo ve vývojovém počítači pomocí F5.

Otevřete okno Hledání přehledů aplikací ve Visual Studiu a nastavte ho tak, aby zobrazovalo události z vaší aplikace. Při ladění to můžete provést kliknutím na tlačítko Application Insights.

![Klikněte pravým tlačítkem myši na projekt a zvolte Application Insights, Open.](./media/asp-net-exceptions/34.png)

Všimněte si, že můžete filtrovat sestavu zobrazit pouze výjimky.

*Žádné výjimky zobrazeny? Viz [Zachycení výjimek](#exceptions).*

Kliknutím na sestavu výjimky zobrazíte trasování zásobníku.
Kliknutím na odkaz na řádek v trasování zásobníku otevřete příslušný soubor kódu.

V kódu všimněte si, že CodeLens zobrazuje data o výjimkách:

![CodeLens oznámení o výjimkách.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnostika selhání pomocí portálu Azure
Application Insights je dodáván s kurátorem Prostředí APM, které vám pomůže diagnostikovat selhání ve sledovaných aplikacích. Chcete-li začít, klikněte na možnost Selhání v nabídce prostředků Application Insights, která se nachází v části Prozkoumat.
Měli byste vidět zobrazení na celé obrazovce, které zobrazuje trendy míry selhání pro vaše požadavky, kolik z nich selhává a kolik uživatelů je ovlivněno. Vpravo uvidíte některé z nejužitečnějších distribucí specifických pro vybranou operaci selhání, včetně prvních tří kódů odpovědí, prvních tří typů výjimek a prvních tří typů selhání závislostí.

![Zobrazení třídění selhání (karta Operace)](./media/asp-net-exceptions/failures0719.png)

Jedním kliknutím pak můžete zkontrolovat reprezentativní vzorky pro každou z těchto podmnožiny operací. Zejména pro diagnostiku výjimek můžete kliknout na počet konkrétní výjimky, která má být předložena na kartě Podrobnosti o transakci mezi koncovými místy, například na tuto:

![Karta Podrobnosti o transakcích od konce](./media/asp-net-exceptions/end-to-end.png)

**Alternativně** namísto pohledu na výjimky konkrétní selhání operace, můžete začít z celkového zobrazení výjimek, přepnutím na kartu Výjimky v horní části. Zde si můžete prohlédnout všechny výjimky shromážděné pro monitorovnou aplikaci.

*Žádné výjimky zobrazeny? Viz [Zachycení výjimek](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Vlastní trasování a data protokolu
Chcete-li získat diagnostická data specifická pro vaši aplikaci, můžete vložit kód pro odeslání vlastních telemetrických dat. To se zobrazí v diagnostickém vyhledávání vedle požadavku, zobrazení stránky a dalších automaticky shromážděných dat.

Máte několik možností:

* [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) se obvykle používá pro sledování vzorců využití, ale data, která odešle také zobrazí v části vlastní události v diagnostické vyhledávání. Události jsou pojmenovány a mohou přenášet vlastnosti řetězce a číselné metriky, na kterých můžete [filtrovat diagnostická hledání](../../azure-monitor/app/diagnostic-search.md).
* [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) umožňuje odesílat delší data, jako jsou informace POST.
* [TrackException()](#exceptions) odesílá trasování zásobníku. [Další informace o výjimkách](#exceptions).
* Pokud již používáte architekturu protokolování, jako je Log4Net nebo NLog, můžete [tyto protokoly zachytit](asp-net-trace-logs.md) a zobrazit je v diagnostickém hledání spolu s daty požadavku a výjimky.

Chcete-li tyto události zobrazit, otevřete [hledání](../../azure-monitor/app/diagnostic-search.md) v levé nabídce, vyberte rozevírací nabídku **Typy událostí**a pak zvolte Vlastní událost, Trasování nebo Výjimka.

![Podrobná analýza](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Pokud vaše aplikace generuje mnoho telemetrických dat, sníží modul adaptivního vzorkování automaticky objem dat odesílaných na portál tím, že budou odesílány pouze reprezentativní vzorky událostí. Události, které jsou součástí stejné operace, budou vybrány nebo odznačovány jako skupina, takže můžete procházet mezi souvisejícími událostmi. [Další informace o vzorkování.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Jak zobrazit data požadavku POST
Podrobnosti požadavku nezahrnují data odeslaná do vaší aplikace v volání POST. Chcete-li mít tato data hlášena:

* [Nainstalujte sadu SDK](../../azure-monitor/app/asp-net.md) do projektu aplikace.
* Vložte do aplikace kód pro volání [microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace). Odešlete data POST v parametru zprávy. Existuje omezení povolené velikosti, takže byste se měli pokusit odeslat pouze základní data.
* Při zkoumání neúspěšný požadavek, najít přidružené trasování.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a>Zachytávání výjimek a souvisejících diagnostických dat
Zpočátku se na portálu nezobrazí všechny výjimky, které způsobují chyby ve vaší aplikaci. Zobrazí se všechny výjimky prohlížeče (pokud používáte [javascriptovou sdk na](../../azure-monitor/app/javascript.md) svých webových stránkách). Ale většina výjimky serveru jsou zachyceny službou IIS a budete muset napsat trochu kódu vidět.

Můžete:

* **Protokolovat výjimky explicitně** vložením kódu do obslužných rutin výjimek pro hlášení výjimek.
* **Automatické zachycení výjimek** konfigurací ASP.NET rámci. Potřebné dodatky se liší pro různé typy architektury.

## <a name="reporting-exceptions-explicitly"></a>Explicitní vykazování výjimek
Nejjednodušší způsob je vložit volání TrackException() v obslužné rutině výjimky.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

Vlastnosti a parametry měření jsou volitelné, ale jsou užitečné pro [filtrování a přidávání](../../azure-monitor/app/diagnostic-search.md) dalších informací. Máte-li například aplikaci, která může spouštět několik her, můžete najít všechny zprávy o výjimkách související s určitou hrou. Do každého slovníku můžete přidat libovolný počet položek.

## <a name="browser-exceptions"></a>Výjimky prohlížečů
Většina prohlížeč výjimky jsou hlášeny.

Pokud webová stránka obsahuje soubory skriptů ze sítí pro doručování obsahu ```crossorigin="anonymous"```nebo jiných domén, ujistěte se, že značka skriptu má atribut a že server odesílá [hlavičky CORS](https://enable-cors.org/). To vám umožní získat trasování zásobníku a podrobnosti pro neošetřené výjimky JavaScript z těchto prostředků.

## <a name="reuse-your-telemetry-client"></a>Opětovné použití klienta telemetrie

> [!NOTE]
> TelemetryClient se doporučuje vytvořit instanci jednou a znovu použít po celou dobu životnosti aplikace.

Níže je příklad pomocí TelemetryClient správně.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Webové formuláře
Pro webové formuláře modul HTTP bude moci shromažďovat výjimky, pokud neexistují žádné přesměrování nakonfigurované s CustomErrors.

Pokud však máte aktivní přesměrování, přidejte do Application_Error funkce následující řádky v Global.asax.cs. (Pokud soubor Global.asax ještě nemáte, přidejte soubor Global.asax.)

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```
## <a name="mvc"></a>MVC
Počínaje Application Insights Web SDK verze 2.6 (beta3 a novější), Application Insights shromažďuje neošetřené výjimky vyvolaných v MVC 5+ řadiče metody automaticky. Pokud jste dříve přidali vlastní obslužnou rutinu ke sledování těchto výjimek (jak je popsáno v následujících příkladech), můžete ji odebrat, abyste zabránili dvojitému sledování výjimek.

Existuje několik případů, které filtry výjimek nelze zpracovat. Například:

* Výjimky vyzývané z konstruktorů řadiče.
* Výjimky vyzývané z obslužných rutin zpráv.
* Výjimky vyvolány během směrování.
* Výjimky vyzvaté během serializace obsahu odpovědi.
* Výjimka vyvolána během spuštění aplikace.
* Výjimka vyvolána v úlohách na pozadí.

Všechny výjimky *zpracované* aplikací je stále nutné sledovat ručně.
Neošetřené výjimky pocházející z řadičů obvykle za následek 500 "Vnitřní chyba serveru" odpověď. Pokud je tato odpověď ručně vytvořena v důsledku zpracované výjimky (nebo vůbec žádná výjimka), je sledována v odpovídající telemetrii požadavku s `ResultCode` 500, ale application insights SDK není schopen sledovat odpovídající výjimku.

### <a name="prior-versions-support"></a>Podpora předchozích verzí
Pokud používáte MVC 4 (a předchozí) Application Insights Web SDK 2.5 (a předchozí), naleznete v následujících příkladech sledovat výjimky.

Pokud je `Off`konfigurace [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) , budou k dispozici výjimky pro [shromažďování modulu HTTP.](https://msdn.microsoft.com/library/ms178468.aspx) Pokud je `RemoteOnly` však (výchozí) nebo `On`, výjimka bude vymazána a není k dispozici pro application insights automaticky shromažďovat. Můžete to opravit přepsáním [třídy System.Web.Mvc.HandleErrorAttribute](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)a použitím potlačené třídy, jak je znázorněno pro různé verze MVC níže ([zdroj GitHub](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
Nahraďte atribut HandleError novým atributem v řadičích.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Ukázka](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Zaregistrujte se `AiHandleErrorAttribute` jako globální filtr v Global.asax.cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Ukázka](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Zaregistrujte atribut AiHandleErrorAttribute jako globální filtr v FilterConfig.cs:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Ukázka](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Web API
Počínaje application insights Web SDK verze 2.6 (beta3 a novější), Application Insights shromažďuje neošetřené výjimky vyvolaných v metodách řadiče automaticky pro WebAPI 2+. Pokud jste dříve přidali vlastní obslužnou rutinu ke sledování těchto výjimek (jak je popsáno v následujících příkladech), můžete ji odebrat, abyste zabránili dvojitému sledování výjimek.

Existuje několik případů, které filtry výjimek nelze zpracovat. Například:

* Výjimky vyzývané z konstruktorů řadiče.
* Výjimky vyzývané z obslužných rutin zpráv.
* Výjimky vyvolány během směrování.
* Výjimky vyzvaté během serializace obsahu odpovědi.
* Výjimka vyvolána během spuštění aplikace.
* Výjimka vyvolána v úlohách na pozadí.

Všechny výjimky *zpracované* aplikací je stále nutné sledovat ručně.
Neošetřené výjimky pocházející z řadičů obvykle za následek 500 "Vnitřní chyba serveru" odpověď. Pokud je tato odpověď ručně vytvořena v důsledku zpracované výjimky (nebo vůbec žádná výjimka), `ResultCode` je sledována v odpovídající telemetrii požadavku s 500, ale application insights SDK není schopen sledovat odpovídající výjimku.

### <a name="prior-versions-support"></a>Podpora předchozích verzí
Pokud používáte WebAPI 1 (a předchozí) application insights web SDK 2.5 (a předchozí), naleznete v následujících příkladech ke sledování výjimek.

#### <a name="web-api-1x"></a>Webové rozhraní API 1.x
Přepsat system.web.http.filters.ExceptionFilterAttribute:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

Tento přepsaný atribut můžete přidat do určitých řadičů nebo jej přidat do konfigurace globálního filtru ve třídě WebApiConfig:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[Ukázka](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Webové rozhraní API 2.x
Přidejte implementaci IExceptionLogger:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Přidejte to do služeb v aplikaci WebApiConfig:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[Ukázka](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Jako alternativy můžete:

1. Nahraďte pouze ExceptionHandler vlastní implementaci IExceptionHandler. To se nazývá pouze v případě, že rozhraní framework je stále schopen zvolit, kterou zprávu odpovědi odeslat (ne při přerušení připojení například)
2. Filtry výjimek (jak je popsáno v části o webových api 1.x řadiče výše) - není volána ve všech případech.

## <a name="wcf"></a>WCF
Přidejte třídu, která rozšiřuje Atribut a implementuje IErrorHandler a IServiceBehavior.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Ukázka](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Čítače výkonu výjimek
Pokud jste na server [nainstalovali agenta Application Insights,](../../azure-monitor/app/monitor-performance-live-website-now.md) můžete získat graf s rychlostí výjimek měřenou rozhraním .NET. To zahrnuje zpracované i neošetřené výjimky rozhraní .NET.

Otevřete kartu Průzkumník metrik, přidejte nový graf a vyberte **Míra výjimek**, která je uvedena v části Čítače výkonu.

Rozhraní .NET vypočítá sazbu počítáním počtu výjimek v intervalu a vydělením délkou intervalu.

To se liší od počtu výjimek vypočítaného portálem Application Insights, který počítá sestavy TrackException. Vzorkovací intervaly se liší a sada SDK neodesílá sestavy TrackException pro všechny zpracované a neošetřené výjimky.

## <a name="next-steps"></a>Další kroky
* [Monitorování rest, SQL a dalších volání závislostí](../../azure-monitor/app/asp-net-dependencies.md)
* [Sledování časů načítání stránky, výjimek prohlížeče a volání AJAX](../../azure-monitor/app/javascript.md)
* [Sledování čítačů výkonu](../../azure-monitor/app/performance-counters.md)
