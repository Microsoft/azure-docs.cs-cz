---
title: Automatizace vlastních sestav pomocí Azure Application Insights dat
description: Automatizace vlastních denních, týdenních nebo měsíčních sestav pomocí Azure Application Insights dat
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: f35b7cbde7b719a91b23b19481ab704c445485cb
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827740"
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

* [Automatizace sestav pomocí Microsoft Flow](../platform/logicapp-flow-connector.md)
* [Automatizace sestav pomocí Logic Apps](automate-with-logic-apps.md)
* V scénáři monitorování použijte šablonu [Azure Functions](../../azure-functions/functions-create-first-azure-function.md) "Application Insights naplánovaný výtah". Tato funkce používá SendGrid k doručování e-mailů. 

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

1. Vytvoří Function App Azure. (Application Insights on se vyžaduje jenom _v_ případě, že chcete monitorovat nové Function App s Application Insights).

   Informace o tom, jak [vytvořit aplikaci Function App](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app) , najdete v dokumentaci k Azure Functions.

2. Po dokončení nasazení nového Function App vyberte **Přejít k prostředku**.

3. Vyberte možnost **Nová funkce**.

   ![Vytvoření snímku nové funkce](./media/automate-custom-reports/new-function.png)

4. Vyberte **_Application Insights naplánovanou šablonu Digest_**.

     > [!NOTE]
     > Ve výchozím nastavení se aplikace Function App vytvoří s modulem runtime verze 3. x. Aby bylo možné použít šablonu Application Insights naplánovaného algoritmu Digest, je třeba [cílit na Azure Functions runtime verze](../../azure-functions/set-runtime-version.md) **1. x** . Chcete-li změnit verzi modulu runtime, přejděte na konfigurační > Function runtime nastavení. ![snímek obrazovky za běhu](./media/automate-custom-reports/change-runtime-v.png)

   ![Snímek nové Application Insights šablony funkce](./media/automate-custom-reports/function-app-04.png)

5. Zadejte příslušnou e-mailovou adresu příjemce pro sestavu a vyberte **vytvořit**.

   ![Snímek obrazovky s nastavením funkce](./media/automate-custom-reports/scheduled-digest.png)

6. Vyberte **Function App**  >  **konfiguraci funkcí platformy**  >  **Configuration**.

    ![Snímek obrazovky nastavení aplikace Azure Functions](./media/automate-custom-reports/config.png)

7. Vytvořte tři nová nastavení aplikace s odpovídajícími odpovídajícími hodnotami ``AI_APP_ID`` , ``AI_APP_KEY`` a ``SendGridAPI`` . Vyberte **Uložit**.

     ![Snímek rozhraní pro integraci funkce](./media/automate-custom-reports/app-settings.png)
    
    (Hodnoty AI_ najdete v části přístup k rozhraní API pro prostředek Application Insights, pro který chcete vykázat. Pokud nemáte Application Insights klíč rozhraní API, je k dispozici možnost **vytvořit klíč rozhraní API**.)
    
   * AI_APP_ID = ID aplikace
   * AI_APP_KEY = klíč rozhraní API
   * SendGridAPI = klíč rozhraní API SendGrid

     > [!NOTE]
     > Pokud nemáte účet SendGrid, můžete ho vytvořit. SendGrid dokumentace k Azure Functions je [zde](../../azure-functions/functions-bindings-sendgrid.md). Pokud stačí pouze minimální vysvětlení, jak nastavit SendGrid a vygenerovat klíč rozhraní API, je k dispozici na konci tohoto článku. 

8. Vyberte možnost **integrace** a ve výstupu klikněte na **SendGrid ($Return)**.

     ![Snímek obrazovky výstupu](./media/automate-custom-reports/integrate.png)

9. V **nastavení aplikace SendGridAPI Key**vyberte nově vytvořené nastavení aplikace pro **SendGridAPI**.

     ![Spustit Function App snímek obrazovky](./media/automate-custom-reports/sendgrid-output.png)

10. Spusťte a otestujte Function App.

     ![Snímek obrazovky testu](./media/automate-custom-reports/function-app-11.png)

11. Zkontrolujte e-mail a potvrďte, že zpráva byla úspěšně odeslána nebo přijata.

     ![Snímek obrazovky řádku předmětu e-mailu](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid s Azure

Tyto kroky platí pouze v případě, že ještě nemáte nakonfigurovaný účet SendGrid.

1. V Azure Portal vyberte **vytvořit prostředek** > vyhledejte **doručování e-mailů SendGrid** > klikněte na **vytvořit** > vyplňte pokyny pro vytvoření SendGrid specifické pro vytváření.

     ![Vytvoření snímku SendGrid prostředku](./media/automate-custom-reports/sendgrid.png)

2. Po vytvoření v části účty SendGrid vyberte **Spravovat**.

     ![Snímek obrazovky s nastavením klíče rozhraní API](./media/automate-custom-reports/sendgrid-manage.png)

3. Tím se spustí web SendGrid. Vyberte **Nastavení**  >  **klíče rozhraní API**.

     ![Snímek obrazovky pro vytvoření a zobrazení aplikace klíč rozhraní API](./media/automate-custom-reports/function-app-15.png)

4. Vytvořte klíč rozhraní API > vyberte **vytvořit & zobrazení**. (Přečtěte si dokumentaci k SendGrid s omezeným přístupem a určete, jakou úroveň oprávnění je vhodná pro váš klíč rozhraní API. Úplný přístup se tady vybere jenom pro účely příkladu.)

   ![Snímek obrazovky s úplným přístupem](./media/automate-custom-reports/function-app-16.png)

5. Zkopírujte celý klíč, tato hodnota je to, co potřebujete v nastavení Function App, jako hodnotu pro SendGridAPI.

   ![Snímek obrazovky s kopií klíče rozhraní API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o vytváření [analytických dotazů](../log-query/get-started-queries.md).
* Další informace o [programovém dotazování Application Insights dat](https://dev.applicationinsights.io/)
* Další informace o [Logic Apps](../../logic-apps/logic-apps-overview.md).
* Přečtěte si další informace o [Microsoft Power automatizuje](https://ms.flow.microsoft.com).

