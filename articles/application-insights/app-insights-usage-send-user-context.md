---
title: Odeslání kontextu uživatele ID umožňující použití prostředí ve službě Azure Application Insights | Dokumentace Microsoftu
description: Sledujte, jak se uživatelé přesunou prostřednictvím služby tak, že každý z nich přiřadíte jedinečný a trvalý řetězec ID ve službě Application Insights.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/02/2017
ms.reviewer: abgreg;mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: bfb04f596a61ff79c75cd38473c9480a29b0e6c4
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139886"
---
#  <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Odeslání kontextu uživatele ID Umožněte využití ve službě Azure Application Insights

## <a name="tracking-users"></a>Sledování uživatelů

Application Insights umožňuje monitorovat a sledovat uživatele přes sadu nástrojů pro využití produktu: 
* [Uživatelé, relace, události](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [Trychtýře](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [Uchování](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* Kohorty
* [Workbooks](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Aby bylo možné sledovat, co uživatel provede v čase, Application Insights musí ID pro každého uživatele nebo relace. Zahrňte následující ID každé vlastní událost nebo zobrazení stránky.
- Uživatelé, trychtýře, uchovávání a kohorty: zahrnují ID uživatele.
- Relace: Zahrnují ID relace.

Pokud vaše aplikace je integrováno [JavaScript SDK](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), se automaticky sleduje ID uživatele.

## <a name="choosing-user-ids"></a>Výběr ID uživatele

ID uživatelů byste neměli zachovat napříč uživatelských relací ke sledování chování uživatelů v čase. Existují různé přístupy k uchování ID.
- Definice jako uživatel, který už máte ve své službě.
- Služba má přístup do prohlížeče, ho můžete předat do souboru cookie s ID prohlížeče v ní. ID se zachová pro za předpokladu, zůstane soubor cookie v prohlížeči uživatele.
- V případě potřeby můžete použít nové ID každé relaci, ale výsledky o uživatelích budou omezené. Například nebudete moci zobrazit, jak mění chování uživatele v průběhu času.

ID by měl být identifikátor Guid nebo jiným řetězcem dostatečně složité jedinečnou identifikaci jednotlivých uživatelů. Například to může být dlouhý náhodné číslo.

Obsahuje-li ID osobní identifikační údaje o uživateli, není odpovídající hodnotu k odeslání do Application Insights jako ID uživatele. Můžete odeslat toto ID jako [ověřit ID uživatele](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), ale nesplňuje požadavek na ID uživatele pro scénáře použití.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET apps: nastavení uživatelský kontext ITelemetryInitializer

Vytvořte inicializátoru telemetrie, jak je popsáno podrobněji [tady](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer)a nastavte Context.User.Id a Context.Session.Id.

V tomto příkladu nastaví ID uživatele pro identifikátor, jejíž platnost vyprší po relaci. Pokud je to možné použijte ID uživatele, které se uchovávají napříč relacemi.

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
            // For a full experience, track each user across sessions. For an incomplete view of user 
            // behavior within a session, store user ID on the HttpContext Session.
            // Set the user ID if we haven't done so yet.
            if (HttpContext.Current.Session["UserId"] == null)
            {
                HttpContext.Current.Session["UserId"] = Guid.NewGuid();
            }

            // Set the user id on the Application Insights telemetry item.
            telemetry.Context.User.Id = (string)HttpContext.Current.Session["UserId"];

            // Set the session id on the Application Insights telemetry item.
            telemetry.Context.Session.Id = HttpContext.Current.Session.SessionID;
        }
      }
    }
```

## <a name="next-steps"></a>Další postup
- Povolit použití prostředí, spusťte odesílání [vlastních událostí](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud jste již posílat vlastní události nebo zobrazení stránek, prozkoumejte nástroje využití se dozvíte, jak uživatelé vaši službu používat.
    * [Přehled využití](app-insights-usage-overview.md)
    * [Uživatelé, relace a události](app-insights-usage-segmentation.md)
    * [Trychtýře](usage-funnels.md)
    * [Uchování](app-insights-usage-retention.md)
    * [Workbooks](app-insights-usage-workbooks.md)
