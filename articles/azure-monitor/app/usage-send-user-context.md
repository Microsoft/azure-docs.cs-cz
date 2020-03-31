---
title: ID kontextu uživatele ke sledování aktivity – Přehledy aplikací Azure
description: Sledujte, jak uživatelé procházejí vaší službou přiřazením každého z nich jedinečný, trvalý řetězec ID v Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: beb5a0f7ad3733aaf12b0880af4fba23a705a7e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670929"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Odeslání ID kontextu uživatele, která umožní možnosti využití v Přehledech aplikací Azure

## <a name="tracking-users"></a>Sledování uživatelů

Application Insights umožňuje sledovat a sledovat uživatele prostřednictvím sady nástrojů pro používání produktů:

- [Uživatelé, relace, události](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Trychtýře](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Uchovávání informací](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) Kohort
- [Workbooks](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Aby bylo možné sledovat, co uživatel dělá v průběhu času, Application Insights potřebuje ID pro každého uživatele nebo relace. Do každé vlastní události nebo zobrazení stránky zahrňte následující ID.

- Uživatelé, cesty, retence a kohorty: Uveďte ID uživatele.
- Relace: Zahrnout ID relace.

> [!NOTE]
> Toto je pokročilý článek popisující ruční kroky pro sledování aktivity uživatelů pomocí Application Insights. U mnoha webových aplikací **nemusí být tyto kroky vyžadovány**, protože výchozí sady SDK na straně serveru ve spojení s [sadou JavaScript SDK na straně klienta/prohlížeče](../../azure-monitor/app/website-monitoring.md )jsou často dostatečné k automatickému sledování aktivity uživatelů. Pokud jste nenakonfigurovali [monitorování na straně klienta](../../azure-monitor/app/website-monitoring.md ) kromě sady SDK na straně serveru, proveďte nejprve a vyzkoušejte, zda nástroje analýzy chování uživatelů fungují podle očekávání.

## <a name="choosing-user-ids"></a>Výběr ID uživatelů

ID uživatelů by měla přetrvávat v rámci uživatelských relací, aby bylo možné sledovat, jak se uživatelé v průběhu času chovají. Existují různé přístupy pro zachování ID.

- Definice uživatele, který již máte ve své službě.
- Pokud má služba přístup k prohlížeči, může prohlížeči předat soubor cookie s ID. ID bude přetrvávat tak dlouho, dokud soubor cookie zůstane v prohlížeči uživatele.
- V případě potřeby můžete použít nové ID každé relace, ale výsledky o uživatelích budou omezené. Například nebudete moci vidět, jak se chování uživatele v průběhu času mění.

ID by měl být Guid nebo jiný řetězec komplex dostatečně identifikovat každého uživatele jedinečně. Může to být například dlouhé náhodné číslo.

Pokud ID obsahuje osobní identifikační údaje o uživateli, není vhodnou hodnotou pro odeslání do Application Insights jako ID uživatele. Takové ID můžete odeslat jako [ověřené ID uživatele](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), ale nesplňuje požadavek ID uživatele pro scénáře použití.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET aplikace: Nastavení uživatelského kontextu v ITelemetryInitializer

Vytvořte inicializátor telemetrie, jak je podrobně popsáno [zde](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer). PředajI ID relace prostřednictvím telemetrie požadavku a nastavte Context.User.Id a Context.Session.Id.

Tento příklad nastaví ID uživatele na identifikátor, který vyprší po relaci. Pokud je to možné, použijte ID uživatele, které přetrvává napříč relacemi.

### <a name="telemetry-initializer"></a>Inicializátor telemetrie

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Soubor Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>Další kroky

- Chcete-li povolit možnosti využití, začněte odesílat [vlastní události](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud už vlastní události nebo zobrazení stránek odesíláte, prozkoumejte nástroje využití a zjistěte, jak uživatelé vaši službu používají.
    - [Přehled použití](usage-overview.md)
    - [Uživatelé, relace a události](usage-segmentation.md)
    - [Trychtýře](usage-funnels.md)
    - [Uchovávání](usage-retention.md)
    - [Workbooks](../../azure-monitor/app/usage-workbooks.md)
