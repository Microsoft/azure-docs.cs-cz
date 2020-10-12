---
title: ID kontextů uživatele pro sledování aktivity – Azure Application Insights
description: Sledujte, jak uživatelé přecházejí přes vaši službu tím, že jim přiřadí jedinečný a trvalý řetězec ID v Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: 46b7479df6d087915cfe81895a786a528da6b9bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87327901"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Odeslání ID kontextů uživatele pro povolení prostředí využití v Azure Application Insights

## <a name="tracking-users"></a>Sledování uživatelů

Application Insights vám umožní monitorovat a sledovat uživatele pomocí sady nástrojů pro použití produktů:

- [Uživatelé, relace, události](./usage-segmentation.md)
- [Trychtýře](./usage-funnels.md)
- [Uchovávání informací](./usage-retention.md) Kohorty
- [Workbooks](../platform/workbooks-overview.md)

Aby bylo možné sledovat, co uživatel pracuje v průběhu času, Application Insights potřebovat ID pro každého uživatele nebo relaci. Do každé vlastní události nebo zobrazení stránky zahrňte následující ID.

- Uživatelé, trychtýře, uchovávání a kohorty: zahrnují ID uživatele.
- Relace: zahrňte ID relace.

> [!NOTE]
> Toto je pokročilý článek s přehledem ručních kroků sledování aktivity uživatelů pomocí Application Insights. V případě mnoha webových aplikací se **tyto kroky nemusí vyžadovat**, protože výchozí sady SDK na straně serveru ve spojení se sadou [SDK na straně klienta nebo prohlížeče JavaScript](./website-monitoring.md)jsou často dostačující k automatickému sledování aktivity uživatelů. Pokud jste ještě nenakonfigurovali [monitorování na straně klienta](./website-monitoring.md) kromě sady SDK na straně serveru, proveďte nejprve test a zjistěte, jestli se nástroje pro analýzu chování uživatelů provádějí podle očekávání.

## <a name="choosing-user-ids"></a>Výběr ID uživatelů

ID uživatelů by měla být trvalá napříč uživatelskými relacemi a sledovat, jak se uživatelé budou chovat v průběhu času. Existují různé přístupy k uchování ID.

- Definice uživatele, který ve vaší službě již máte.
- Pokud má služba přístup k prohlížeči, může předat do prohlížeče soubor cookie s ID. ID se uchová po delší dobu, než soubor cookie zůstane v prohlížeči uživatele.
- V případě potřeby můžete pro každou relaci použít nové ID, ale výsledky pro uživatele budou omezené. Například nebudete moct zjistit, jak se chování uživatele v průběhu času mění.

ID by mělo být identifikátor GUID nebo jiný řetězec, který je dostatečně složitý pro identifikaci každého uživatele jedinečně. Může to být třeba dlouhé náhodné číslo.

Pokud ID obsahuje osobní identifikační údaje uživatele, není vhodná hodnota pro odeslání do Application Insights jako ID uživatele. Takové ID můžete odeslat jako [ID ověřeného uživatele](./api-custom-events-metrics.md#authenticated-users), ale nesplňuje požadavky na ID uživatele pro scénáře používání.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>Aplikace ASP.NET: nastavení kontextu uživatele v ITelemetryInitializer

Vytvořte inicializátor telemetrie, jak je popsáno [zde](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer). Předejte ID relace prostřednictvím telemetrie požadavků a nastavte Context.User.Id a Context.Session.Id.

Tento příklad nastaví ID uživatele na identifikátor, jehož platnost vyprší po relaci. Pokud je to možné, použijte ID uživatele, které trvá napříč relacemi.

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

- Chcete-li povolit prostředí používání, začněte odesílat [vlastní události](./api-custom-events-metrics.md#trackevent) nebo [zobrazení stránek](./api-custom-events-metrics.md#page-views).
- Pokud jste už odeslali vlastní události nebo zobrazení stránky, prozkoumejte nástroje využití a zjistěte, jak uživatelé používají vaši službu.
    - [Přehled využití](usage-overview.md)
    - [Uživatelé, relace a události](usage-segmentation.md)
    - [Trychtýře](usage-funnels.md)
    - [Uchovávání](usage-retention.md)
    - [Workbooks](../platform/workbooks-overview.md)

