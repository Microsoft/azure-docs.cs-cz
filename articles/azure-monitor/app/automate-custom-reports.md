---
title: Automatizace vlastních sestav pomocí Azure Application Insights dat
description: Automatizace vlastních denních, týdenních nebo měsíčních sestav pomocí Azure Application Insights dat
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: d91595a863901fcc420611ac644c7856e74320dd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655119"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizace vlastních sestav pomocí Azure Application Insights dat

Periodické sestavy vám pomohou informovat tým o tom, jak fungují důležité služby pro podnikání. Vývojáři, týmy DevOps/SRE a jejich manažeři můžou být produktivní díky automatizovaným sestavám spolehlivého poskytování přehledů, aniž by se museli přihlašovat na portálu. Tyto sestavy mohou také pomáhat identifikovat postupné zvýšení latencí, zátěže nebo frekvence selhání, které nemusí aktivovat žádná pravidla výstrahy.

Každá organizace má své jedinečné požadavky na vytváření sestav, jako například: 

* Konkrétní součty percentilu metrik nebo vlastní metriky v sestavě
* Mají různé sestavy pro každodenní, týdenní a měsíční souhrn dat pro různé cílové skupiny.
* Segmentace podle vlastních atributů, jako je oblast nebo prostředí. 
* Seskupte některé prostředky AI společně v jedné sestavě, i když můžou být v různých předplatných nebo skupinách prostředků atd.
* Samostatné sestavy obsahující citlivé metriky odeslané na selektivní cílovou skupinu.
* Sestavování účastníkům, kteří by neměli mít přístup k prostředkům portálu.

> [!NOTE] 
> Týdenní Application Insightsé e-mailové zprávy o Digest neumožňují žádné přizpůsobení a budou se dál vycházet z dalších vlastních možností uvedených níže. Poslední týdenní e-mail Digest se pošle 11. června 2018. Nakonfigurujte jednu z následujících možností, abyste získali podobné vlastní sestavy (použijte dotaz navržený níže).

## <a name="to-automate-custom-report-emails"></a>Automatizace vlastních e-mailů sestav

[Pomocí programového dotazování](https://dev.applicationinsights.io/) na data Application Insights můžete vytvářet vlastní sestavy podle plánu. Následující možnosti vám pomůžou rychle začít:

* [Automatizace sestav pomocí Microsoft Flow](automate-with-flow.md)
* [Automatizace sestav pomocí Logic Apps](automate-with-logic-apps.md)
* V scénáři monitorování použijte šablonu [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) "Application Insights naplánovaný výtah". Tato funkce používá SendGrid k doručování e-mailů. 

    ![Šablona funkce Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Ukázkový dotaz na týdenní e-mail s hodnotou hash
Následující dotaz ukazuje spojení více datových sad pro týdenní e-mailovou zprávu o vydigest, jako je sestava. Přizpůsobte si ho podle potřeby a použijte ho s kteroukoli z výše uvedených možností a automatizujte týdenní sestavu.   

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

## <a name="application-insights-scheduled-digest-report"></a>Sestava naplánovaných Digest Application Insights

1. Z Azure Portal vyberte **vytvořit prostředek** > **COMPUTE** > **Function App**.

   ![Vytvoření prostředku Azure Function App snímku obrazovky](./media/automate-custom-reports/function-app-01.png)

2. Zadejte příslušné informace pro vaši aplikaci a vyberte _vytvořit_. (Application Insights _on_ se vyžaduje jenom v případě, že chcete monitorovat nové Function App s Application Insights).

   ![Vytvoření snímku obrazovky nastavení Function App prostředků Azure](./media/automate-custom-reports/function-app-02.png)

3. Po dokončení nasazení nového Function App vyberte **Přejít k prostředku**.

4. Vyberte možnost **Nová funkce**.

   ![Vytvoření snímku nové funkce](./media/automate-custom-reports/function-app-03.png)

5. Vyberte **_Application Insights naplánovanou šablonu Digest_** .

     > [!NOTE]
     > Ve výchozím nastavení se aplikace Function App vytvoří s modulem runtime verze 2. x. Aby bylo možné použít šablonu Application Insights naplánovaného algoritmu Digest, je třeba [cílit na Azure Functions runtime verze](https://docs.microsoft.com/azure/azure-functions/set-runtime-version) **1. x** .  snímek obrazovky ![runtime](./../../../includes/media/functions-view-update-version-portal/function-app-view-version.png)



   ![Snímek nové Application Insights šablony funkce](./media/automate-custom-reports/function-app-04.png)

6. Zadejte příslušnou e-mailovou adresu příjemce pro sestavu a vyberte **vytvořit**.

   ![Snímek obrazovky s nastavením funkce](./media/automate-custom-reports/function-app-05.png)

7. Vyberte > **funkce platformy** **Function App** > **nastavení aplikace**.

    ![Snímek obrazovky nastavení aplikace Azure Functions](./media/automate-custom-reports/function-app-07.png)

8. Vytvořte tři nová nastavení aplikace s příslušnými odpovídajícími hodnotami ``AI_APP_ID``, ``AI_APP_KEY``a ``SendGridAPI``. Vyberte **Save** (Uložit).

     ![Snímek rozhraní pro integraci funkce](./media/automate-custom-reports/function-app-08.png)
    
    (Hodnoty AI_ najdete v části přístup k rozhraní API pro prostředek Application Insights, pro který chcete vykázat. Pokud nemáte Application Insights klíč rozhraní API, je k dispozici možnost **vytvořit klíč rozhraní API**.)
    
   * AI_APP_ID = ID aplikace
   * AI_APP_KEY = klíč rozhraní API
   * SendGridAPI = klíč rozhraní API SendGrid

     > [!NOTE]
     > Pokud nemáte účet SendGrid, můžete ho vytvořit. SendGrid dokumentace k Azure Functions je [zde](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Pokud stačí pouze minimální vysvětlení, jak nastavit SendGrid a vygenerovat klíč rozhraní API, je k dispozici na konci tohoto článku. 

9. Vyberte možnost **integrace** a ve výstupu klikněte na **SendGrid ($Return)** .

     ![Snímek obrazovky výstupu](./media/automate-custom-reports/function-app-09.png)

10. V **nastavení aplikace SendGridAPI Key**vyberte nově vytvořené nastavení aplikace pro **SendGridAPI**.

     ![Spustit Function App snímek obrazovky](./media/automate-custom-reports/function-app-010.png)

11. Spusťte a otestujte Function App.

     ![Snímek obrazovky testu](./media/automate-custom-reports/function-app-11.png)

12. Zkontrolujte e-mail a potvrďte, že zpráva byla úspěšně odeslána nebo přijata.

     ![Snímek obrazovky řádku předmětu e-mailu](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid s Azure

Tyto kroky platí pouze v případě, že ještě nemáte nakonfigurovaný účet SendGrid.

1. V Azure Portal vyberte **vytvořit prostředek** vyhledávání pro **doručování e-mailů** , > klikněte na **vytvořit** > a vyplňte pokyny pro vytvoření specifické pro SendGrid. 

     ![Vytvoření snímku SendGrid prostředku](./media/automate-custom-reports/function-app-13.png)

2. Po vytvoření v části účty SendGrid vyberte **Spravovat**.

     ![Snímek obrazovky s nastavením klíče rozhraní API](./media/automate-custom-reports/function-app-14.png)

3. Tím se spustí web SendGrid. Vyberte **nastavení** > **klíče rozhraní API**.

     ![Snímek obrazovky pro vytvoření a zobrazení aplikace klíč rozhraní API](./media/automate-custom-reports/function-app-15.png)

4. Vytvořte klíč rozhraní API > zvolte **vytvořit & zobrazení** (Přečtěte si prosím dokumentaci SendGrid pro omezený přístup a určete, jakou úroveň oprávnění je vhodná pro váš klíč rozhraní API. Úplný přístup se tady vybere jenom pro účely příkladu.)

   ![Snímek obrazovky s úplným přístupem](./media/automate-custom-reports/function-app-16.png)

5. Zkopírujte celý klíč, tato hodnota je to, co potřebujete v nastavení Function App, jako hodnotu pro SendGridAPI.

   ![Snímek obrazovky s kopií klíče rozhraní API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o vytváření [analytických dotazů](../../azure-monitor/log-query/get-started-queries.md).
* Další informace o [programovém dotazování Application Insights dat](https://dev.applicationinsights.io/)
* Další informace o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Přečtěte si další informace o [Microsoft Flow](https://ms.flow.microsoft.com).
