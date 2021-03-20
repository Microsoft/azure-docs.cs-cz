---
title: Diagnostikujte selhání a výjimky pomocí Azure Application Insights
description: Zachyťte výjimky z aplikací ASP.NET spolu s telemetrie žádostí.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/11/2019
ms.openlocfilehash: 36e916eabfca8e997fc3d46ff10f6201203457cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936499"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnostika výjimky ve webových aplikacích pomocí služby Application Insights
Výjimky v živé webové aplikaci jsou hlášeny [Application Insights](./app-insights-overview.md). Neúspěšné žádosti s výjimkami a dalšími událostmi můžete korelovat na straně klienta i serveru, abyste mohli rychle diagnostikovat příčiny.

## <a name="set-up-exception-reporting"></a>Nastavení generování sestav výjimek
* Aby byly výjimky hlášené z aplikace serveru:
  * Azure Web Apps: přidání [rozšíření Application Insights](./azure-web-apps.md)
  * Virtuální počítače Azure a škálování sady virtuálních počítačů Azure – aplikace hostované službou IIS: přidat [rozšíření monitorování aplikací](./azure-vm-vmss-apps.md)
  * Nainstalujte [sadu Application Insights SDK](./asp-net.md) do kódu aplikace nebo
  * Webové servery služby IIS: spustit [Application Insights agenta](./monitor-performance-live-website-now.md); ani
  * Java Web Apps: povolení [agenta Java](./java-in-process-agent.md)
* Nainstalujte [fragment kódu jazyka JavaScript](./javascript.md) do webových stránek pro zachycení výjimek prohlížeče.
* V některých aplikačních architekturách nebo s některými nastaveními musíte provést několik kroků navíc, abyste mohli zachytit další výjimky:
  * [Webové formuláře](#web-forms)
  * [MVC](#mvc)
  * [Webové rozhraní API 1. *](#web-api-1x)
  * [Webové rozhraní API 2. *](#web-api-2x)
  * [WCF](#wcf)

  Tento článek se zaměřuje konkrétně na .NET Framework aplikace z pohledu kódu. Některé metody, které fungují pro .NET Framework, jsou v .NET Core SDK zastaralé. Pokud máte aplikaci .NET Core, přečtěte si [dokumentaci .NET Core SDK](./asp-net-core.md) .

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnostika výjimek pomocí sady Visual Studio
Otevřete řešení aplikace v aplikaci Visual Studio, které vám pomůžou s laděním.

Spusťte aplikaci, a to buď na vašem serveru, nebo na svém vývojovém počítači pomocí klávesy F5.

Otevřete okno Application Insights vyhledávání v sadě Visual Studio a nastavte ho tak, aby zobrazoval události z vaší aplikace. Když ladíte, můžete to udělat pouhým kliknutím na tlačítko Application Insights.

![Klikněte pravým tlačítkem na projekt a vyberte Application Insights, otevřít.](./media/asp-net-exceptions/34.png)

Všimněte si, že sestavu můžete filtrovat a zobrazit jenom výjimky.

*Nezobrazuje se žádná výjimka? Viz [zachytit výjimky](#exceptions).*

Kliknutím na sestavu výjimky zobrazíte její trasování zásobníku.
Kliknutím na odkaz na řádek v trasování zásobníku otevřete příslušný soubor kódu.

V kódu si všimněte, že CodeLens zobrazuje data o výjimkách:

![CodeLens upozornění na výjimky.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnostika selhání pomocí Azure Portal
Application Insights poskytuje s využitím prostředí APM, které vám umožní diagnostikovat chyby ve monitorovaných aplikacích. Spusťte kliknutím na možnost chyby v nabídce Application Insights prostředku, která se nachází v části prozkoumat.
Mělo by se zobrazit zobrazení na celé obrazovce, které vám ukáže trendy při selhání vašich požadavků, kolik z nich selhává a kolik uživatelů má vliv. Napravo se zobrazí některé z nejužitečnějších distribucí, které jsou specifické pro vybranou operaci selhání, včetně hlavních tří kódů odpovědí, hlavních tří typů výjimek a hlavních tří neúspěšných typů závislostí.

![Zobrazení třídění podle selhání (karta operace)](./media/asp-net-exceptions/failures0719.png)

Jediným kliknutím si můžete prohlédnout reprezentativní ukázky pro každou z těchto dílčích sad operací. Konkrétně pro diagnostiku výjimek můžete kliknout na počet konkrétních výjimek, které se mají zobrazit na kartě Podrobnosti transakce od začátku až do konce, jako je například tato:

![Karta podrobnosti transakce na konci](./media/asp-net-exceptions/end-to-end.png)

Místo toho **,** abyste si vyhledali výjimky konkrétní operace selhání, můžete začít z celkového zobrazení výjimek, a to přepnutím na kartu výjimky v horní části. Tady vidíte všechny shromažďované výjimky pro monitorovanou aplikaci.

*Nezobrazuje se žádná výjimka? Viz [zachytit výjimky](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Vlastní trasování a data protokolu
Chcete-li získat diagnostická data specifická pro vaši aplikaci, můžete vložit kód, který bude odesílat vlastní data telemetrie. Zobrazuje se v diagnostickém vyhledávání společně se žádostí, zobrazením stránky a dalšími automaticky shromážděnými daty.

Máte několik možností:

* [TrackEvent ()](./api-custom-events-metrics.md#trackevent) se obvykle používá pro monitorování vzorců používání, ale data, která posílá, se zobrazí také v části vlastní události v diagnostickém vyhledávání. Události se nazývají a můžou mít vlastnosti řetězce a číselné metriky, na kterých můžete [filtrovat hledání v diagnostice](./diagnostic-search.md).
* [TrackTrace ()](./api-custom-events-metrics.md#tracktrace) umožňuje odesílat delší data, jako jsou například informace o publikování.
* [TrackException ()](#exceptions) odesílá trasování zásobníku. [Další informace o výjimkách](#exceptions).
* Pokud již používáte protokolovací rozhraní jako Log4Net nebo NLog, můžete [tyto protokoly zachytit](asp-net-trace-logs.md) a zobrazit je ve vyhledávání diagnostiky společně s daty o požadavcích a výjimkách.

Chcete-li zobrazit tyto události, otevřete [vyhledávání](./diagnostic-search.md) v nabídce vlevo, vyberte **typy událostí** rozevírací nabídky a pak zvolte vlastní událost, trasování nebo výjimka.

![Podrobná analýza](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Pokud vaše aplikace generuje mnoho telemetrických dat, sníží modul adaptivního vzorkování automaticky objem dat odesílaných na portál tím, že budou odesílány pouze reprezentativní vzorky událostí. Události, které jsou součástí stejné operace, se vyberou nebo zruší výběr jako skupina, takže můžete přecházet mezi souvisejícími událostmi. [Přečtěte si o vzorkování.](./sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Jak zobrazit data žádosti o odeslání
Podrobnosti žádosti neobsahují data odesílaná do aplikace v příspěvku. Pokud chcete mít tato data nahlášená:

* [Nainstalujte sadu SDK](./asp-net.md) do projektu aplikace.
* Do aplikace vložte kód pro volání [Microsoft. ApplicationInsights. TrackTrace ()](./api-custom-events-metrics.md#tracktrace). Odešlete data POST v parametru zprávy. Povolená velikost je omezena, proto byste se měli pokusit odeslat jenom základní data.
* Při prošetření neúspěšného požadavku Najděte přidružená trasování.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a> Zachycování výjimek a souvisejících diagnostických dat
Nejdříve se na portálu nezobrazí všechny výjimky, které způsobují chyby ve vaší aplikaci. Zobrazí se všechny výjimky prohlížeče (Pokud používáte [sadu JavaScript SDK](./javascript.md) na webových stránkách). Ale většina výjimek serveru je zachycena službou IIS a je nutné napsat bitovou kopii pro jejich zobrazení.

Další možnosti:

* **Protokolujte výjimky explicitně** vložením kódu do obslužných rutin výjimek pro hlášení výjimek.
* **Automatické zachycení výjimek** konfigurací architektury ASP.NET Nezbytné doplňky jsou odlišné pro různé typy rozhraní.

## <a name="reporting-exceptions-explicitly"></a>Explicitní generování výjimek
Nejjednodušší způsob je vložení volání TrackException () do obslužné rutiny výjimky.

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

Parametry vlastností a měření jsou volitelné, ale jsou užitečné pro [filtrování a přidání](./diagnostic-search.md) dalších informací. Například pokud máte aplikaci, která může spustit několik her, můžete najít všechny zprávy o výjimkách, které souvisejí s určitou hrou. Do každého slovníku můžete přidat tolik položek, kolik jich chcete.

## <a name="browser-exceptions"></a>Výjimky prohlížečů
Je nahlášena většina výjimek prohlížeče.

Pokud vaše webová stránka obsahuje soubory skriptu ze sítě pro doručování obsahu nebo jiné domény, ujistěte se, že značka skriptu má atribut ```crossorigin="anonymous"``` a že server posílá [hlavičky CORS](https://enable-cors.org/). To vám umožní získat trasování zásobníku a podrobnosti o neošetřených výjimkách JavaScriptu z těchto prostředků.

## <a name="reuse-your-telemetry-client"></a>Opětovné použití klienta telemetrie

> [!NOTE]
> TelemetryClient se doporučuje vytvořit instanci jednou a znovu použít po celou dobu životnosti aplikace.

Níže je příklad použití TelemetryClient správně.

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
V případě webových formulářů bude modul HTTP moci shromažďovat výjimky, pokud nejsou nakonfigurována žádná přesměrování nakonfigurovaná pomocí CustomErrors.

Pokud ale máte aktivní přesměrování, přidejte následující řádky do funkce Application_Error v Global. asax. cs. (Přidejte soubor Global. asax, pokud ho ještě nemáte.)

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
Počínaje verzí Application Insights web SDK verze 2,6 (beta3 a novější) Application Insights shromažďuje neošetřené výjimky vyvolané v metodách řadičů MVC 5 + automaticky. Pokud jste dříve přidali vlastní obslužnou rutinu ke sledování takových výjimek (jak je popsáno v následujících příkladech), je možné ji odebrat, aby nedocházelo k dvojímu sledování výjimek.

Existuje několik případů, kdy filtry výjimek nemůžou zpracovat. Například:

* Výjimky vyvolané konstruktory kontrolerů
* Výjimky vyvolané obslužnými rutinami zpráv
* Výjimky vyvolané během směrování
* Výjimky vyvolané během serializace obsahu odpovědi
* Výjimky vyvolané během spouštění aplikace
* Výjimky vyvolané úlohami na pozadí

Všechny výjimky, které aplikace *zpracovává* , je stále nutné sledovat ručně.
Neošetřené výjimky, které pocházejí z řadičů, obvykle způsobují odpověď 500 "interní chyba serveru". Pokud je taková odpověď manuálně vytvořená jako výsledek ošetřené výjimky (nebo žádná výjimka vůbec), je sledována v odpovídající telemetrii žádostí s `ResultCode` 500, ale Application Insights SDK nemůže sledovat odpovídající výjimku.

### <a name="prior-versions-support"></a>Podpora předchozích verzí
Pokud používáte MVC 4 (a předchozí) Application Insights web SDK 2,5 (a předchozí), Sledujte výjimky v následujících příkladech.

Pokud je konfigurace [customErrors](/previous-versions/dotnet/netframework-4.0/h0hfz6fc(v=vs.100)) `Off` , budou k dispozici výjimky pro [modul HTTP](/previous-versions/dotnet/netframework-3.0/ms178468(v=vs.85)) ke shromáždění. Pokud je však `RemoteOnly` (výchozí) nebo `On` , bude výjimka vymazána a nebude k dispozici pro Application Insights pro automatické shromáždění. Můžete to opravit přepsáním [třídy System. Web. Mvc. HandleErrorAttribute](/dotnet/api/system.web.mvc.handleerrorattribute?view=aspnet-mvc-5.2)a použitím přepsané třídy, jak je znázorněno pro různé verze MVC níže ([zdroj GitHubu](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

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
Zaregistrujte `AiHandleErrorAttribute` se jako globální filtr v Global. asax. cs:

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
Zaregistrujte AiHandleErrorAttribute jako globální filtr v FilterConfig. cs:

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

## <a name="web-api"></a>Webové rozhraní API
Počínaje verzí Application Insights web SDK verze 2,6 (beta3 a novější) Application Insights shromažďuje neošetřené výjimky, které jsou vyvolány v metodách kontroleru automaticky pro WebAPI 2 +. Pokud jste dříve přidali vlastní obslužnou rutinu ke sledování takových výjimek (jak je popsáno v následujících příkladech), je možné ji odebrat, aby nedocházelo k dvojímu sledování výjimek.

Existuje několik případů, kdy filtry výjimek nemůžou zpracovat. Například:

* Výjimky vyvolané konstruktory kontrolerů
* Výjimky vyvolané obslužnými rutinami zpráv
* Výjimky vyvolané během směrování
* Výjimky vyvolané během serializace obsahu odpovědi
* Výjimky vyvolané během spouštění aplikace
* Výjimky vyvolané úlohami na pozadí

Všechny výjimky, které aplikace *zpracovává* , je stále nutné sledovat ručně.
Neošetřené výjimky, které pocházejí z řadičů, obvykle způsobují odpověď 500 "interní chyba serveru". Pokud je taková odpověď manuálně vytvořená jako výsledek ošetřené výjimky (nebo žádná výjimka vůbec), je sledována v odpovídající telemetrii žádosti s `ResultCode` 500, ale Application Insights SDK nemůže sledovat odpovídající výjimku.

### <a name="prior-versions-support"></a>Podpora předchozích verzí
Pokud používáte WebAPI 1 (a předchozí) Application Insights web SDK 2,5 (a předchozí), Sledujte výjimky v následujících příkladech.

#### <a name="web-api-1x"></a>Webové rozhraní API 1. x
Přepsat System. Web. http. filters. ExceptionFilterAttribute:

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

Tento přepsaný atribut můžete přidat na konkrétní řadiče nebo ho přidat do konfigurace globálního filtru ve třídě WebApiConfig:

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

#### <a name="web-api-2x"></a>Webové rozhraní API 2. x
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

Přidejte ho ke službám v WebApiConfig:

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

1. Nahraďte pouze ExceptionHandler vlastní implementací IExceptionHandler. Tato metoda se volá jenom v případě, že rozhraní stále dokáže zvolit, která odpověď se má odeslat (ne když je připojení přerušené).
2. Filtry výjimek (jak je popsáno v části na řadičích webového rozhraní API 1. x výše) – nevolá se ve všech případech.

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

## <a name="exception-performance-counters"></a>Čítače výkonu výjimek
Pokud jste na server [nainstalovali agenta Application Insights](./monitor-performance-live-website-now.md) , můžete získat graf četnosti výjimek měřených rozhraním .NET. To zahrnuje jak zpracované, tak neošetřené výjimky rozhraní .NET.

Otevřete kartu Průzkumník metrik, přidejte nový graf a vyberte možnost **míra výjimek**, která je uvedena v části čítače výkonu.

Rozhraní .NET Framework vypočítá sazbu tak, že spočítá počet výjimek v intervalu a vydělí délku intervalu.

To se liší od počtu Exceptions vypočítaného Application Insights portálem pro počítání sestav TrackException. Intervaly vzorkování se liší a sada SDK neodesílá sestavy TrackException pro všechny zpracovávané a neošetřené výjimky.

## <a name="next-steps"></a>Další kroky
* [Monitorování REST, SQL a dalších volání závislostí](./asp-net-dependencies.md)
* [Sledování doby načítání stránek, výjimek prohlížeče a volání AJAX](./javascript.md)
* [Monitorování čítačů výkonu](./performance-counters.md)

