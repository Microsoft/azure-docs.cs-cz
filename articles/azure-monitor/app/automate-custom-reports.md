---
title: Automatizace vlastních sestav pomocí dat Azure Application Insights
description: Automatizace vlastních denních/týdenních/měsíčních přehledů pomocí dat Azure Application Insights
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: d91595a863901fcc420611ac644c7856e74320dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655119"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizace vlastních sestav pomocí dat Azure Application Insights

Pravidelné sestavy pomáhají informovat tým o tom, jak si vedou důležité obchodní služby. Vývojáři, týmy DevOps/SRE a jejich manažeři mohou být produktivní díky automatizovaným sestavám, které spolehlivě poskytují přehledy, aniž by museli všichni portál přihlásit. Tyto sestavy mohou také pomoci identifikovat postupné zvyšování latence, zatížení nebo selhání, které nemusí vyvolat žádná pravidla výstrah.

Každý podnik má své jedinečné potřeby vykazování, například: 

* Konkrétní agregace percentilů metrik nebo vlastních metrik v přehledu.
* Měj různé přehledy pro denní, týdenní a měsíční souhrny dat pro různé okruhy uživatelů.
* Segmentace podle vlastních atributů, jako je oblast nebo prostředí. 
* Seskupte některé prostředky AI do jedné sestavy, i když mohou být v různých předplatných nebo skupinách prostředků atd.
* Samostatné přehledy obsahující citlivé metriky odeslané selektivnímu publiku.
* Sestavy zúčastněným stranám, které nemusí mít přístup k prostředkům portálu.

> [!NOTE] 
> Týdenní Application Insights digest e-mail neumožnil žádné přizpůsobení, a bude ukončena ve prospěch vlastních možností uvedených níže. Poslední týdenní digest e-mail bude odeslán v červnu 11, 2018. Chcete-li získat podobné vlastní sestavy, nakonfigurujte jednu z následujících možností (použijte níže navržený dotaz).

## <a name="to-automate-custom-report-emails"></a>Automatizace vlastních e-mailů sestav

Můžete [programově dotazovat](https://dev.applicationinsights.io/) data Application Insights a generovat vlastní sestavy podle plánu. Následující možnosti vám mohou pomoci rychle začít:

* [Automatizace sestav pomocí Microsoft Flow](automate-with-flow.md)
* [Automatizace sestav pomocí aplikací Logika](automate-with-logic-apps.md)
* Použijte šablonu [funkce Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) "Application Insights scheduled digest" ve scénáři monitorování. Tato funkce používá SendGrid k doručení e-mailu. 

    ![Šablona funkce Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Ukázkový dotaz pro týdenní e-mail digest
Následující dotaz ukazuje spojení mezi více datových sad pro týdenní digest e-mail, jako je sestava. Přizpůsobte ji podle potřeby a použijte ji s některou z výše uvedených možností k automatizaci týdenní sestavy.   

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

## <a name="application-insights-scheduled-digest-report"></a>Sestava naplánovaného přehledu aplikace Přehledy

1. Na portálu Azure vyberte Vytvořit**aplikaci pro****výpočetní** > funkce **prostředků** > .

   ![Vytvoření aplikace Azure Resource Function App – snímek obrazovky](./media/automate-custom-reports/function-app-01.png)

2. Zadejte příslušné informace pro aplikaci a vyberte _Vytvořit_. (Application Insights _On_ je vyžadováno pouze v případě, že chcete sledovat novou aplikaci funkcí pomocí Application Insights)

   ![Vytvoření obrazovky nastavení aplikace Azure Resource Function App](./media/automate-custom-reports/function-app-02.png)

3. Po dokončení nasazení nové aplikace funkce vyberte **Přejít na prostředek**.

4. Vyberte **novou funkci**.

   ![Vytvoření nového snímku obrazovky funkce](./media/automate-custom-reports/function-app-03.png)

5. Vyberte **_šablonu plánovače redigu aplikace_**.

     > [!NOTE]
     > Ve výchozím nastavení jsou aplikace funkcí vytvářeny pomocí runtime verze 2.x. Chcete-li použít naplánovanou šablonu digest, musíte cílit na [runtime verze](https://docs.microsoft.com/azure/azure-functions/set-runtime-version) **1.x** funkce Azure Functions.  ![runtime snímek obrazovky](./../../../includes/media/functions-view-update-version-portal/function-app-view-version.png)



   ![Snímek obrazovky Se šablonou nové aplikace](./media/automate-custom-reports/function-app-04.png)

6. Zadejte příslušnou e-mailovou adresu příjemce sestavy a vyberte **možnost Vytvořit**.

   ![Nastavení funkce – snímek obrazovky](./media/automate-custom-reports/function-app-05.png)

7. Vyberte funkce **platformy** > **funkce nastavení** > **aplikace**.

    ![Snímek obrazovky nastavení aplikace Azure](./media/automate-custom-reports/function-app-07.png)

8. Vytvořte tři nová nastavení ``AI_APP_ID``aplikace ``AI_APP_KEY``s ``SendGridAPI``odpovídajícími odpovídajícími hodnotami , a . Vyberte **Uložit**.

     ![Snímek obrazovky rozhraní pro integraci funkcí](./media/automate-custom-reports/function-app-08.png)
    
    (Hodnoty AI_ najdete v části Přístup k rozhraní API pro prostředek Application Insights, o kterém chcete nahlásit. Pokud nemáte klíč rozhraní API Application Insights, je možnost **vytvořit klíč rozhraní API**.)
    
   * AI_APP_ID = ID aplikace
   * AI_APP_KEY = Klíč rozhraní API
   * SendGridAPI =Klíč rozhraní API SendGrid

     > [!NOTE]
     > Pokud nemáte účet SendGrid, můžete jej vytvořit. SendGrid dokumentace pro funkce Azure je [zde](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Pokud jen chcete minimální vysvětlení, jak nastavit SendGrid a generovat klíč rozhraní API jeden je k dispozici na konci tohoto článku. 

9. Vyberte **Integrovat** a v části Výstupy klikněte na **SendGrid ($return).**

     ![Výstupní snímek obrazovky](./media/automate-custom-reports/function-app-09.png)

10. V části **Nastavení aplikace klíče SendGridAPI**vyberte nově vytvořené nastavení aplikace pro **SendGridAPI**.

     ![Snímek obrazovky aplikace Spustit funkci](./media/automate-custom-reports/function-app-010.png)

11. Spusťte a otestujte aplikaci funkce.

     ![Snímek obrazovky testu](./media/automate-custom-reports/function-app-11.png)

12. Zkontrolujte e-mail a ověřte, zda byla zpráva úspěšně odeslána/přijata.

     ![Snímek obrazovky předmětu e-mailu](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid s Azure

Tyto kroky platí pouze v případě, že ještě nemáte nakonfigurovaný účet SendGrid.

1. Na portálu Azure vyberte **Vytvořit** hledání prostředků pro **sendgrid doručování e-mailů** > klikněte **na vytvořit** > a vyplňte sendgrid konkrétní vytvořit pokyny. 

     ![Vytvořit snímek obrazovky zdroje SendGrid](./media/automate-custom-reports/function-app-13.png)

2. Po vytvoření v části Účty SendGrid vyberte **Spravovat**.

     ![Snímek obrazovky s klíčem API nastavení](./media/automate-custom-reports/function-app-14.png)

3. Tím se spustí SendGrid stránky. Vyberte **možnost Nastavení** > **klíčů rozhraní API**.

     ![Snímek obrazovky aplikace Vytvořit a zobrazit klíč rozhraní API](./media/automate-custom-reports/function-app-15.png)

4. Vytvořte klíč rozhraní API > zvolte **Vytvořit & zobrazení** (Zkontrolujte dokumentaci SendGrid o omezeném přístupu a zjistěte, jaká úroveň oprávnění je vhodná pro váš klíč rozhraní API. Úplný přístup je zde vybrán například pouze pro účely.)

   ![Snímek obrazovky s úplným přístupem](./media/automate-custom-reports/function-app-16.png)

5. Zkopírujte celý klíč, tato hodnota je to, co potřebujete v nastavení aplikace funkce jako hodnota pro SendGridAPI

   ![Snímek obrazovky s klíčem Kopírovat rozhraní API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o vytváření [dotazů analytics](../../azure-monitor/log-query/get-started-queries.md).
* Další informace o [programovém dotazování na data Application Insights](https://dev.applicationinsights.io/)
* Další informace o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Další informace o [Microsoft Flow](https://ms.flow.microsoft.com).
