---
title: Diagnostikujte chyby a výjimky ve službě web apps s využitím Azure Application Insights | Dokumentace Microsoftu
description: Zachytit výjimky z aplikací ASP.NET spolu s telemetrie žádostí.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: 76ce5f0c44dd1985fbf47d493b4566634f4894d0
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976677"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnostika výjimky ve vašich webových aplikací pomocí Application Insights
Výjimky ve vaší živé webové aplikace jsou hlášeny sadou [Application Insights](../../application-insights/app-insights-overview.md). Neúspěšné požadavky můžete korelovat s výjimky a dalších událostí na klienta a serveru, tak, že můžete rychle Diagnostikujte příčiny.

## <a name="set-up-exception-reporting"></a>Nastavení generování sestav výjimky
* K výjimkám hlášené z aplikací serveru:
  * Nainstalujte [Application Insights SDK](../../azure-monitor/app/asp-net.md) v kódu aplikace, nebo
  * Webové servery služby IIS: Spustit [agenta Application Insights](../../azure-monitor/app/monitor-performance-live-website-now.md); nebo
  * Azure web apps: Přidat [rozšíření Application Insights](../../azure-monitor/app/azure-web-apps.md)
  * Webové aplikace v Javě: Nainstalujte [agenta Java](../../azure-monitor/app/java-agent.md)
* Nainstalujte [fragment kódu jazyka JavaScript](../../azure-monitor/app/javascript.md) na webových stránkách jak zachytávat výjimky prohlížeče.
* V některých aplikační architektury, nebo s některými nastaveními je potřeba udělat některé dodatečné kroky, jak zachytávat výjimky Další:
  * [Webové formuláře](#web-forms)
  * [MVC](#mvc)
  * [1.* webového rozhraní API](#web-api-1x)
  * [2.* webového rozhraní API](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnostika výjimek pomocí sady Visual Studio
Otevřete aplikaci řešení v sadě Visual Studio k pomoci s laděním.

Spusťte aplikaci, na vašem serveru nebo na počítači pro vývoj pomocí klávesy F5.

Otevření okna hledání Application Insights v sadě Visual Studio a nastavte ho chcete zobrazit události z vaší aplikace. Při ladění, můžete to provést jednoduše kliknutím na tlačítko Application Insights.

![Klikněte pravým tlačítkem na projekt a vyberte Application Insights, otevřete.](./media/asp-net-exceptions/34.png)

Všimněte si, že můžete filtrovat sestavu pro zobrazení jenom výjimky.

*Žádné výjimky, zobrazuje? Zobrazit [zachycení výjimek](#exceptions).*

Klikněte na tlačítko sestava zobrazíte jeho trasování zásobníku výjimek.
Klikněte na tlačítko s odkazem na řádek v trasování zásobníku, otevřete soubor odpovídající kód.

V kódu Všimněte si, že CodeLens ukazuje údaje o výjimky:

![CodeLens oznámení o výjimkách.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnostikování chyb pomocí webu Azure portal
Application Insights přichází s APM způsob při diagnostice chyb ve vašich monitorovaných aplikacích. Pokud chcete začít, klikněte na možnost selhání v nabídce prostředků Application Insights v části prozkoumat.
Měli byste vidět zobrazení na celou obrazovku, který zobrazuje trendy míra selhání pro vaše požadavky, kolik z nich se nedaří a kolik uživatelů bylo ovlivněno. Na pravé straně uvidíte některé specifické pro vybranou nejužitečnější distribucí neúspěšné operace, včetně kódy odpovědí první 3, 3 nejpoužívanější typy výjimek a nejčastější 3 typy závislostí služeb při selhání.

![Chyby třídění zobrazení (kartu operace)](./media/asp-net-exceptions/FailuresTriageView.png)

Jedním kliknutím můžete potom zkontrolovat reprezentativní vzorky pro každou z těchto podmnožin operací. Diagnostikovat výjimky, zejména, můžete kliknout na počet konkrétní výjimky, které se budou zobrazovat s okno s podrobnostmi o výjimky, jako je například tento:

![Okno s podrobnostmi o výjimce](./media/asp-net-exceptions/ExceptionDetailsBlade.png)

**Alternativně** namísto pohledu na výjimky, které konkrétní neúspěšnou operaci, můžete spustit z celkového přehledu o výjimkách, přepnutím na kartu výjimky:

![Chyby třídění zobrazení (kartu výjimky)](./media/asp-net-exceptions/FailuresTriageView_Exceptions.png)

Zde můžete zobrazit všechny výjimky, které jsou shromažďovány pro monitorované aplikace.

*Žádné výjimky, zobrazuje? Zobrazit [zachycení výjimek](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Vlastní trasování a data protokolu
K získání diagnostických dat specifické pro vaši aplikaci, můžete vložit kód pro odesílání vlastních telemetrických dat. To zobrazuje v diagnostickém vyhledávání vedle požadavku, zobrazení stránky a další data se automaticky shromažďují.

Máte několik možností:

* [Funkce TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) se obvykle používá pro monitorování vzorce používání, ale také odesílá data se zobrazí v části vlastní události v diagnostickém vyhledávání. Události jsou pojmenovány a mohou obsahovat vlastnosti řetězce a číselnou metriky, na kterém je možné [filtrovat diagnostických hledání](../../azure-monitor/app/diagnostic-search.md).
* [Použitím metod TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) vám umožní odeslat delší data, jako jsou informace o příspěvku.
* [Metody TrackException()](#exceptions) odešle trasování zásobníku. [Další informace o výjimkách](#exceptions).
* Pokud už používáte protokolovací rozhraní, jako jsou Log4Net nebo NLog, můžete si [zaznamenat tyto protokoly](asp-net-trace-logs.md) a zobrazit je v diagnostickém vyhledávání vedle dat požadavků a výjimek.

Chcete-li zobrazit tyto události, otevřete [hledání](../../azure-monitor/app/diagnostic-search.md), otevřete filtr a pak zvolte vlastní události, trasování a výjimky.

![Procházet na podrobnosti](./media/asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> Pokud vaše aplikace generuje mnoho telemetrických dat, sníží modul adaptivního vzorkování automaticky objem dat odesílaných na portál tím, že budou odesílány pouze reprezentativní vzorky událostí. Události, které jsou součástí stejné operace bude zaškrtnuto nebo nevybrány jako skupina, takže mohou procházet mezi souvisejícími událostmi. [Další informace o vzorkování.](../../application-insights/app-insights-sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Jak zobrazit data požadavku POST
Podrobnosti o žádosti jsou data odeslaná do vaší aplikace v rámci volání POST. Aby tato data ohlásil:

* [Instalace sady SDK](../../azure-monitor/app/asp-net.md) v projektu aplikace.
* Vložení kódu v aplikaci volat [Microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace). Odeslat příspěvek data v parametru zprávy. Platí omezení na povolenou velikost, snažte se odeslat jenom základní data.
* Při zkoumání neúspěšného požadavku najdete související trasování.

![Procházet na podrobnosti](./media/asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a> Zachycení výjimky a související diagnostických dat
Zpočátku nezobrazí na portálu všechny výjimky, které způsobí selhání ve vaší aplikaci. Zobrazí se vám všechny výjimky prohlížeče (Pokud používáte [JavaScript SDK](../../azure-monitor/app/javascript.md) na webových stránkách). Ale většina serverové výjimky jsou zachyceny službou IIS a budete muset psát hodně kódu k jejich zobrazení.

Můžete:

* **Protokolování výjimek explicitně** tak, že vloží kód do obslužné rutiny výjimek k hlášení výjimek.
* **Zachycení výjimek automaticky** nakonfigurováním ASP.NET framework. Potřebné dodatky se liší pro různé typy rozhraní.

## <a name="reporting-exceptions-explicitly"></a>Explicitně hlášení výjimek
Nejjednodušší způsob je vložit volání pro TrackException() do obslužné rutiny výjimky.

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

Vlastnosti a měření parametry jsou volitelné, ale jsou užitečné pro [filtrování a přidání](../../azure-monitor/app/diagnostic-search.md) dodatečné informace. Pokud máte aplikaci, kterou můžete spustit několik her, například může vyhledat všechny sestavy výjimky související s konkrétním hru. Můžete přidat tolik položek, jako má každý slovník.

## <a name="browser-exceptions"></a>Výjimky prohlížečů
Většina výjimek prohlížeče jsou hlášeny.

Pokud vaše webová stránka obsahuje soubory skriptů ze sítí pro doručování obsahu nebo v jiných doménách, ujistěte se, vaši značku skriptu má atribut ```crossorigin="anonymous"```, a že server odešle [hlavičky CORS](https://enable-cors.org/). To vám umožní získat trasování zásobníku a podrobnosti pro neošetřených výjimek jazyka JavaScript z těchto prostředků.

## <a name="web-forms"></a>Webové formuláře
Pro webové formuláře bude moci shromažďovat výjimky, když nejsou žádná přesměrování nakonfigurovanou CustomErrors modulu protokolu HTTP.

Ale pokud máte aktivní přesměrování, přidejte následující řádky do funkce Application_Error Global.asax.cs. (Pokud ho ještě nemáte, přidejte soubor Global.asax.)

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
Spouští se pomocí Application Insights Web SDK verze 2.6 (beta3 a novější), Application Insights shromažďuje neošetřené výjimky vzniklé v metodách 5 + řadiče MVC automaticky. Pokud jste dříve přidali vlastní obslužnou rutinu ke sledování těchto výjimek (jak je popsáno v následujících příkladech), můžete odebrat zabránili double sledování výjimek.

Existuje několik případů, které nemůže zpracovat filtry výjimek. Příklad:

* Výjimky vyvolané z konstruktorů kontroleru.
* Výjimky vyvolané z obslužné rutiny zpráv.
* Výjimky vyvolané při směrování.
* Výjimky vyvolané během serializace obsahu odpovědí.
* Byla vyvolána výjimka při spuštění aplikace.
* V úlohách na pozadí došlo k výjimce.

Všechny výjimky *zpracovává* aplikací stále potřeba sledovat ručně.
Neošetřených výjimek pocházejících od řadičů obvykle za následek odpovědi 500 "Vnitřní chyba serveru". Pokud takové odpovědi je ručně vytvořený jako výsledek zpracování výjimek (nebo vůbec žádné výjimky) je sledována v odpovídající telemetrie žádostí s `ResultCode` 500, ale není schopen sledovat odpovídající výjimek Application Insights SDK.

### <a name="prior-versions-support"></a>Předchozí verze podporují
Pokud používáte MVC 4 (a předchozí) z Application Insights Web SDK 2.5 (a předchozí), najdete v následujících příkladech ke sledování výjimek.

Pokud [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) konfigurace je `Off`, bude k dispozici pro výjimky [modulu HTTP služby](https://msdn.microsoft.com/library/ms178468.aspx) ke shromažďování. Nicméně pokud je `RemoteOnly` (výchozí), nebo `On`, výjimka bude zaškrtnuté a není k dispozici pro službu Application Insights automaticky shromažďovat. Která můžete opravit tak, že přepíšete [System.Web.Mvc.HandleErrorAttribute třídy](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)a jak je znázorněno pro různé verze MVC níže použití přepsané třídy ([zdroje Githubu](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

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
Atribut HandleError nahraďte nového atributu ve vašich kontrolerech.

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
Zaregistrujte `AiHandleErrorAttribute` jako globální filtr v Global.asax.cs:

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
Zaregistrujte se jako globální filtr v FilterConfig.cs AiHandleErrorAttribute:

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
Spouští se pomocí Application Insights Web SDK verze 2.6 (beta3 a novější), Application Insights shromažďuje neošetřené výjimky vzniklé v metodách kontroleru automaticky pro WebAPI 2 +. Pokud jste dříve přidali vlastní obslužnou rutinu ke sledování těchto výjimek (jak je popsáno v následujících příkladech), můžete odebrat zabránili double sledování výjimek.

Existuje několik případů, které nemůže zpracovat filtry výjimek. Příklad:

* Výjimky vyvolané z konstruktorů kontroleru.
* Výjimky vyvolané z obslužné rutiny zpráv.
* Výjimky vyvolané při směrování.
* Výjimky vyvolané během serializace obsahu odpovědí.
* Byla vyvolána výjimka při spuštění aplikace.
* V úlohách na pozadí došlo k výjimce.

Všechny výjimky *zpracovává* aplikací stále potřeba sledovat ručně.
Neošetřených výjimek pocházejících od řadičů obvykle za následek odpovědi 500 "Vnitřní chyba serveru". Pokud takové odpovědi je ručně vytvořený jako výsledek zpracování výjimek (nebo vůbec žádné výjimky) je sledována v odpovídající telemetrie žádostí s `ResultCode` 500, ale není schopen sledovat odpovídající výjimek Application Insights SDK.

### <a name="prior-versions-support"></a>Předchozí verze podporují
Pokud používáte WebAPI 1 (a předchozí) z Application Insights Web SDK 2.5 (a předchozí), najdete v následujících příkladech ke sledování výjimek.

#### <a name="web-api-1x"></a>Webové rozhraní API 1.x
Override System.Web.Http.Filters.ExceptionFilterAttribute:

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

Může přidat tento atribut přepsané na konkrétní řadiče, nebo ho přidejte do konfigurace globálních filtrů třídy WebApiConfig:

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
Přidáte implementaci nástroje IExceptionLogger:

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

Přidejte ke službám v WebApiConfig následující:

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

1. Nahraďte vlastní implementaci IExceptionHandler pouze ExceptionHandler. Volá se, jenom když rozhraní framework je stále možnost rozhodnout, jaké zprávy s odpovědí na odeslání (při připojení není přerušena pro instanci)
2. Filtry výjimek (jak je popsáno v části o 1.x kontrolerů rozhraní Web API výše) – ve všech případech se nevolala.

## <a name="wcf"></a>WCF
Přidejte třídu, která rozšiřuje atribut a implementuje IErrorHandler a IServiceBehavior.

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

## <a name="exception-performance-counters"></a>Čítače výkonu výjimky
Pokud máte [nainstalovaného agenta Application Insights](../../azure-monitor/app/monitor-performance-live-website-now.md) na serveru, můžete získat graf frekvence výjimek, měřeny pomocí .NET. To zahrnuje zpracovaných a nezpracovaných výjimek .NET.

Otevře se okno Průzkumníku metrik, přidejte nový graf a vyberte **frekvence výjimek**popsaný v části čítače výkonu.

Rozhraní .NET framework se vypočítává míra počítání počet výjimek v intervalu a dělení délku intervalu.

Tím se liší od počtu "Výjimek", počítá pomocí portálu služby Application Insights, počítací TrackException sestavy. Intervalů vzorkování se liší a sada SDK nebude odesílat TrackException sestavy pro všechny ošetřené i neošetřené výjimky.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup
* [Monitorování REST, SQL a další volání závislostí](../../azure-monitor/app/asp-net-dependencies.md)
* [Sledujte dobu načítání stránek, výjimek prohlížeče a volání jazyka AJAX](../../azure-monitor/app/javascript.md)
* [Monitorování čítačů výkonu](../../application-insights/app-insights-performance-counters.md)
