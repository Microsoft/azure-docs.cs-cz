---
title: Automatizace vlastní sestavy s daty Azure Application Insights
description: Automatizace vlastní sestavy denně nebo týdně nebo měsíčně s daty Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 2cda1d0f216678f1dd8c074136ae35683c52d910
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52719670"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizace vlastní sestavy s daty Azure Application Insights

Pravidelné sestavy pomáhají týmu informován o tom, jak jejich obchodní důležité služby jsou. Vývojáři, týmy DevOps/SRE a jejich vedoucími může být produktivní automatizované sestavy spolehlivé doručování insights bez nutnosti všem uživatelům se přihlásit na portál. Tyto zprávy může také pomoct identifikovat postupné zvyšování latence, zatížení nebo Chyba kurzů, které nesmí aktivovat žádné pravidla upozornění.

Každá organizace má jeho jedinečné potřeby generování sestav, například: 

* Agregace konkrétní percentilu metrik, nebo vlastní metriky v sestavě.
* Máte různé sestavy pro denní, týdenní a měsíční shrnutí dat pro různé skupiny uživatelů.
* Segmentace podle vlastních atributů, jako je oblast nebo prostředí. 
* Seskupit několik zdrojů informací, AI v jedné sestavě, i když může být v různých předplatných nebo prostředek skupiny atd.
* Samostatné sestavy obsahující citlivé metriky odesílat selektivní cílovou skupinu.
* Sestavy účastníkům, kteří nemají přístup k portálu prostředkům.

> [!NOTE] 
> Týdenní e-mail digest Application Insights nepovolil jakéhokoli přizpůsobení a bude ukončena a místo toho použití vlastních možností uvedených níže. Poslední týdenní digest e-mail se pošle 11. června 2018. Nakonfigurujte prosím jednu z následujících možností pro získání vlastních sestav podobné (pomocí dotazu navrhované níže).

## <a name="to-automate-custom-report-emails"></a>K automatizaci vlastní sestavy e-mailů

Je možné [programově odeslat dotaz Application Insights](https://dev.applicationinsights.io/) data pro generování vlastních sestav podle plánu. Následující možnosti vám umožňují rychle začít:

* [Automatizace sestavy v Microsoft Flow](app-insights-automate-with-flow.md)
* [Automatizace sestavy s Logic Apps](automate-with-logic-apps.md)
* Použití "Application Insights naplánované výběru" [funkce Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) šablony v případě monitorování. Tato funkce využívá SendGrid k doručování e-mailu. 

    ![Šablona funkce Azure functions](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Ukázkový dotaz pro týdenní e-mailu ověřování algoritmem digest
Následující dotaz zobrazí spojování napříč více datových sad pro týdenní digest e-mail jako zprávu. Přizpůsobení podle potřeby a jeho použití s některou z možností uvedených k automatizaci Týdenní sestava.   

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

## <a name="application-insights-scheduled-digest-report"></a>Sestavy naplánované digest Application Insights

1. Na webu Azure Portal, vyberte **vytvořit prostředek** > **Compute** > **aplikace Function App**.

   ![Vytvoření – snímek obrazovky aplikace funkcí prostředků Azure](./media/automate-custom-reports/function-app-01.png)

2. Zadejte příslušné informace pro vaši aplikaci a vyberte _vytvořit_. (Application Insights _na_ je vyžadován pouze pokud chcete monitorovat vaši novou aplikaci Function App pomocí Application Insights)

   ![Vytvoření – snímek obrazovky nastavení aplikace Function App prostředků Azure](./media/automate-custom-reports/function-app-02.png)

3. Po dokončení nasazení nové aplikace Function App, vyberte **přejít k prostředku**.

4. Vyberte **novou funkci**.

   ![Vytvořit nový snímek obrazovky – funkce](./media/automate-custom-reports/function-app-03.png)

5. Vyberte  **_šablony Application Insights naplánované digest_**.

   ![Nový snímek obrazovky šablony funkce Application Insights](./media/automate-custom-reports/function-app-04.png)

6. Zadejte odpovídající příjemců e-mailovou adresu pro sestavu a vybráním **vytvořit**.

   ![Snímek obrazovky nastavení – funkce](./media/automate-custom-reports/function-app-05.png)

7. Vyberte vaše **aplikace Function App** > **funkce platformy** > **nastavení aplikace**.

    ![Azure snímek obrazovky nastavení aplikace – funkce](./media/automate-custom-reports/function-app-07.png)

8. Vytvořit tři nové nastavení aplikace s odpovídající hodnoty, které odpovídají ``AI_APP_ID``, ``AI_APP_KEY``, a ``SendGridAPI``. Vyberte **Uložit**.

     ![Snímek obrazovky rozhraní integrace – funkce](./media/automate-custom-reports/function-app-08.png)
    
    (Hodnoty AI_ najdete v části přístup přes rozhraní API pro prostředek služby Application Insights chcete v sestavě. Pokud nemáte klíč rozhraní API Application Insights, je možnost **vytvořit klíč rozhraní API**.)
    
    * AI_APP_ID = ID aplikace
    * AI_APP_KEY = klíč rozhraní API
    * SendGridAPI = klíč rozhraní API SendGrid

    > [!NOTE]
    > Pokud nemáte účet SendGrid, že můžete si ho vytvořit. Dokumentace ke službě Sendgridu pro službu Azure Functions je [tady](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Pokud je to jenom má minimální vysvětlení toho, jak nastavit SendGrid a vygenerovat klíč rozhraní API, které je k dispozici na konci tohoto článku. 

9. Vyberte **integrace** a ve skupinovém rámečku výstupy **SendGrid ($return)**.

     ![Snímek obrazovky výstupu](./media/automate-custom-reports/function-app-09.png)

10. V části **nastavení aplikace pro klíč SendGridAPI**, vyberte nově vytvořeného nastavení aplikace pro **SendGridAPI**.

     ![Snímek obrazovky běhu aplikace Function App](./media/automate-custom-reports/function-app-010.png)

11. Spuštění a testování vaší aplikace Function App.

     ![Snímek obrazovky testu](./media/automate-custom-reports/function-app-11.png)

12. Zkontrolujte e-mailu potvrďte, že byla zpráva odeslat/přijmout úspěšně.

     ![Snímek obrazovky řádek předmětu e-mailu](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid s Azure

Tento postup platí pouze pokud ještě nemáte nakonfigurovaný účtu SendGrid.

1. Z portálu Azure vyberte **vytvořit prostředek** vyhledejte **doručování e-mailů SendGrid** > klikněte na tlačítko **vytvořit** > a vyplnit konkrétní SendGrid vytvořit pokyny. 

     ![Vytvoření snímku obrazovky SendGrid prostředků](./media/automate-custom-reports/function-app-13.png)

2. Po vytvoření pod účty SendGrid vyberte **spravovat**.

     ![Snímek obrazovky nastavení klíč rozhraní API](./media/automate-custom-reports/function-app-14.png)

3. Tím se spustí Sendgridu lokality. Vyberte **nastavení** > **klíče rozhraní API**.

     ![Vytvořit a zobrazit klíč rozhraní API aplikace – snímek obrazovky](./media/automate-custom-reports/function-app-15.png)

4. Vytvořte klíč rozhraní API > zvolte **vytvořit & zobrazení** (najdete v dokumentaci Sendgridu na omezený přístup k určení, jaké úroveň oprávnění je vhodný pro svůj klíč rozhraní API. Úplný přístup se tady vyberete pro účely tohoto příkladu pouze.)

   ![Snímek obrazovky s úplným přístupem](./media/automate-custom-reports/function-app-16.png)

5. Zkopírujte celý kód, tato hodnota je nutné v nastavení aplikace Function App jako hodnotu pro SendGridAPI

   ![Zkopírovat snímek klíče rozhraní API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Další postup

* Další informace o vytváření [analytických dotazů](../log-analytics/query-language/get-started-queries.md).
* Další informace o [programově dotazování na data Application Insights](https://dev.applicationinsights.io/)
* Další informace o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Další informace o [Microsoft Flow](https://ms.flow.microsoft.com).
