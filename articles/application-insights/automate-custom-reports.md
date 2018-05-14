---
title: Automatizovat vlastní sestavy s daty Azure Application Insights
description: Automatizovat vlastní sestavy denně nebo týdně nebo měsíčně s daty Azure Application Insights
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizovat vlastní sestavy s daty Azure Application Insights

Pravidelné zprávy udržet tým aktuální informace o úspěšnost své firmy důležité služby. Vývojáři, DevOps/SRE týmů a jejich správce může být produktivní s automatizované sestavy spolehlivě doručování Statistika bez nutnosti všem uživatelům se přihlásit k portálu. Tyto sestavy vám také pomůže zjistit postupná zvyšuje v latenci, zatížení nebo selhání sazby, které nemusí aktivovat žádné výstrahy pravidla.

Každý má podnik jeho jedinečné reporting požadavky, jako například: 

* Konkrétní percentilu agregací metriky nebo vlastní metriky v sestavě.
* Máte různé sestavy pro denní, týdenní a měsíční shrnutí dat pro různé skupiny uživatelů.
* Segmentace podle vlastních atributů, jako je oblast nebo prostředí. 
* Seskupíte některé prostředky AI do jednu sestavu, i když může být v různých předplatných nebo prostředek skupin atd.
* Samostatné sestavy obsahující citlivé metriky posílá selektivní cílovou skupinu.
* Sestavy zúčastněným stranám, kteří nemají přístup k portálu prostředkům.

> [!NOTE] 
> E-mailu týdenní Application Insights digest neumožňuje žádné přizpůsobení a budou zastaveny považuje vlastních možností uvedených níže. Poslední týdenní digest email bude odeslán na 11. června 2018. Nakonfigurujte jeden z následujících možností a získat podobnou vlastní sestavy (použití dotazu navrhované níže).

## <a name="to-automate-custom-report-emails"></a>Pro automatizaci vlastní sestavu e-mailů

Můžete [prostřednictvím kódu programu dotaz Application Insights](https://dev.applicationinsights.io/) data pro generování vlastních sestav podle plánu. Vám rychle začít můžou pomoct následující možnosti:

* [Automatizovat sestavy s Flow Microsoft](app-insights-automate-with-flow.md)
* [Automatizovat sestavy s Logic Apps](automate-with-logic-apps.md)
* Použít daný "Application Insights naplánované výtah" [Azure funkce](https://azure.microsoft.com/services/functions/) šablony ve scénáři monitorování. Tato funkce používá sendgrid vám umožňuje k doručování e-mailu. 

    ![Šablony Azure – funkce](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Ukázkový dotaz pro týdenní ověřování algoritmem digest, e-mailu
Následující dotaz zobrazí, připojení mezi více datových sad pro týdenní e-mail digest jako sestavu. Přizpůsobit podle potřeby a použít ho s žádným z výše uvedených automatizovat týdenní sestavy možností.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>Další postup

- Další informace o vytváření [analytické dotazy](app-insights-analytics-using.md).
- Další informace o [prostřednictvím kódu programu dotazování na data Application Insights](https://dev.applicationinsights.io/)
- Další informace o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
- Další informace o [Microsoft Flow](https://ms.flow.microsoft.com).


