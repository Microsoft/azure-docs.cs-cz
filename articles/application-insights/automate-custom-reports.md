---
title: Automatizovat vlastní sestavy s daty Azure Application Insights
description: Automatizovat vlastní sestavy denně nebo týdně nebo měsíčně s daty Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: c8cff54c67ab2c9c3d09f9261617b6312cc4434a
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025679"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizovat vlastní sestavy s daty Azure Application Insights

Pravidelné zprávy udržet tým aktuální informace o úspěšnost své firmy důležité služby. Vývojáři, DevOps/SRE týmů a jejich správce může být produktivní s automatizované sestavy spolehlivě doručování Statistika bez nutnosti všem uživatelům se přihlásit na portál. Tyto sestavy vám také pomůže zjistit postupná zvyšuje v latenci, zatížení nebo selhání sazby, které nemusí aktivovat žádné výstrahy pravidla.

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
* Použít daný "Application Insights naplánované výtah" [Azure funkce](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) šablony ve scénáři monitorování. Tato funkce používá sendgrid vám umožňuje k doručování e-mailu. 

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

## <a name="application-insights-scheduled-digest-report"></a>Sestava naplánované digest statistiky aplikace

1. Z portálu Azure vyberte **vytvořit prostředek** > **výpočetní** > **aplikaci funkce**.

   ![Vytvoření – snímek obrazovky aplikace funkce prostředků Azure](./media/automate-custom-reports/function-app-01.png)

2. Zadejte příslušné informace pro aplikace a vyberte _vytvořit_. (Application Insights _na_ je vyžadován pouze, pokud chcete monitorování nové aplikace funkce pomocí služby Application Insights)

   ![Vytvoření prostředků Azure, funkce nastavení aplikace – snímek obrazovky](./media/automate-custom-reports/function-app-02.png)

3. Po dokončení nasazení nové aplikace funkce vyberte **přejděte k prostředku**.

4. Vyberte **novou funkci**.

   ![Vytvořte nový snímek – funkce](./media/automate-custom-reports/function-app-03.png)

5. Vyberte  **_Application Insights naplánované digest šablony_**.

   ![Nové šablony přehled funkcí aplikace – snímek obrazovky](./media/automate-custom-reports/function-app-04.png)

6. Zadejte příslušnou příjemce e-mailovou adresu pro sestavu a vyberte **vytvořit**.

   ![Snímek obrazovky nastavení funkce](./media/automate-custom-reports/function-app-05.png)

7. Vyberte vaše **aplikaci funkce** > **funkce** > **nastavení aplikace**.

    ![Azure snímek obrazovky nastavení aplikace – funkce](./media/automate-custom-reports/function-app-07.png)

8. Vytvořte tři nové nastavení aplikace s příslušnými hodnotami odpovídající ``AI_APP_ID``, ``AI_APP_KEY``, a ``SendGridAPI``. Vyberte **Uložit**.

     ![Funkce integrace rozhraní – snímek obrazovky](./media/automate-custom-reports/function-app-08.png)
    
    (Hodnoty AI_ naleznete v části přístup k rozhraní API pro přehledy prostředek aplikace chcete v sestavě. Pokud nemáte klíč rozhraní API Application Insights, je možnost **vytvořit klíč rozhraní API**.)
    
    * AI_APP_ID = ID aplikace
    * AI_APP_KEY = klíč rozhraní API
    * SendGridAPI = sendgrid vám umožňuje klíč rozhraní API

    > [!NOTE]
    > Pokud nemáte účet sendgrid vám umožňuje můžete vytvořit jeden. Dokumentaci sendgrid vám umožňuje na Azure Functions je [zde](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-sendgrid). Pokud je to právě chtít minimální vysvětlení, jak nastavit sendgrid vám umožňuje a vygenerovat klíč rozhraní API, které je k dispozici na konci tohoto článku. 

9. Vyberte **integrací** a v části klikněte na tlačítko výstupy **sendgrid vám umožňuje ($return)**.

     ![Snímek obrazovky výstupu](./media/automate-custom-reports/function-app-09.png)

10. V části **nastavení aplikace klíč SendGridAPI**, vyberte nově vytvořenou nastavení aplikace pro **SendGridAPI**.

     ![Spuštění funkce aplikace – snímek obrazovky](./media/automate-custom-reports/function-app-010.png)

11. Spustit a otestovat funkce aplikace.

     ![Testování – snímek obrazovky](./media/automate-custom-reports/function-app-11.png)

12. Zkontrolujte e-mailu potvrďte, že zprávy úspěšně odeslat/přijmout.

     ![Snímek obrazovky řádek předmětu e-mailu](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>Sendgrid vám umožňuje pomocí Azure

Tento postup platí pouze pokud ještě nemáte účet sendgrid vám umožňuje nakonfigurovat.

1. Na portálu Azure vyberte **vytvořit prostředek** vyhledejte **doručování e-mailem sendgrid vám umožňuje** > klikněte na tlačítko **vytvořit** > a vyplňte na konkrétním sendgrid vám umožňuje vytvořit pokyny. 

     ![Vytvoření prostředku sendgrid vám umožňuje – snímek obrazovky](./media/automate-custom-reports/function-app-13.png)

2. Po vytvoření pod účty sendgrid vám umožňuje vybrat **spravovat**.

     ![Snímek obrazovky nastavení klíč rozhraní API](./media/automate-custom-reports/function-app-14.png)

3. Tím se spustí sendgrid vám umožňuje na webu. Vyberte **nastavení** > **klíče rozhraní API**.

     ![Vytvářet a prohlížet klíč rozhraní API aplikace – snímek obrazovky](./media/automate-custom-reports/function-app-15.png)

4. Vytvořte klíč rozhraní API > vyberte **vytvořit & zobrazení** (přečtěte si dokumentaci sendgrid vám umožňuje společnosti na omezený přístup k určení, jakou úroveň oprávnění je vhodné pro váš klíč rozhraní API. Úplný přístup je zde vybraný příklad pouze pro účely.)

   ![Snímek obrazovky úplný přístup](./media/automate-custom-reports/function-app-16.png)

5. Zkopírujte celý klíč, tato hodnota je co je potřeba v nastavení funkce aplikace jako hodnotu pro SendGridAPI

   ![Zkopírujte snímek klíče rozhraní API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Další postup

* Další informace o vytváření [analytické dotazy](app-insights-analytics-using.md).
* Další informace o [prostřednictvím kódu programu dotazování na data Application Insights](https://dev.applicationinsights.io/)
* Další informace o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Další informace o [Microsoft Flow](https://ms.flow.microsoft.com).
