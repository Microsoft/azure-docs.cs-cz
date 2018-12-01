---
title: Automatizace Azure Application Insights procesů pomocí Logic Apps.
description: Zjistěte, jak rychle procesy můžete automatizovat opakovatelné přidáním konektoru Application Insights do vaší aplikace logiky.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: mbullwin
ms.openlocfilehash: 44e654202cdaebedab82f40f154c1483b7e979ae
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725688"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatizace procesů Application Insights s využitím Logic Apps

Připadá vám sami opakovaného spouštění stejné dotazů na vaše telemetrická data ke kontrole, jestli je vaše služba funguje správně? Pokud chcete automatizovat tyto dotazy pro vyhledání trendy a anomálie a následně vytvořit vlastní pracovní postupy kolem sebe? Azure Application Insights connector (preview) pro Logic Apps je nejvhodnější nástroj pro tento účel.

Pomocí této integrace můžete automatizovat řadu procesů aniž byste museli napsat jediný řádek kódu. Vytvoření aplikace logiky s konektorem služby Application Insights rychle automatizovat jakýkoli proces Application Insights. 

Můžete přidat i další akce. Funkce Logic Apps služby Azure App Service umožňuje stovky akce k dispozici. Například pomocí aplikace logiky můžete automaticky odesílat oznámení e-mailem nebo vytvořit chybu ve službě Azure DevOps. Můžete také použít jeden z mnoha dostupných [šablony](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) chcete urychlit proces tvorby aplikace logiky. 

## <a name="create-a-logic-app-for-application-insights"></a>Vytvoření aplikace logiky pro službu Application Insights

V tomto kurzu se dozvíte, jak vytvořit aplikaci logiky, která používá algoritmus autocluster Analytics skupiny atributů v datech pro webovou aplikaci. Tento tok automaticky odesílá výsledky e-mailem, pouze jeden příklad použití Application Insights Analytics a Logic Apps společně. 

### <a name="step-1-create-a-logic-app"></a>Krok 1: Vytvoření aplikace logiky
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Klikněte na tlačítko **vytvořit prostředek**vyberte **Web + mobilní zařízení**a pak vyberte **aplikace logiky**.

    ![Nové okno aplikace logiky](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Krok 2: Vytvoření aktivační události pro vaši aplikaci logiky
1. V **návrhář aplikace logiky** okně v části **začněte běžně používaným triggerem**vyberte **opakování**.

    ![Okno návrháře aplikace logiky](./media/automate-with-logic-apps/logicapp2.png)

1. V **frekvence** vyberte **den** a pak na **Interval** zadejte **1**.

    ![Návrhář aplikace logiky "Recurrence" okna](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>Krok 3: Přidání akce Application Insights
1. Klikněte na tlačítko **nový krok**a potom klikněte na tlačítko **přidat akci**.

1. V **zvolte akci** vyhledávacího pole, typ **Azure Application Insights**.

1. V části **akce**, klikněte na tlačítko **Azure Application Insights – Analytics vizualizace dotazu ve verzi Preview**.

    !["Zvolte akci" okna návrháře aplikací logiky](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Krok 4: Připojení k prostředku služby Application Insights

K dokončení tohoto kroku potřebujete ID aplikace a klíč rozhraní API pro váš prostředek. Můžete je znovu načíst z portálu Azure portal, jak je znázorněno v následujícím diagramu:

![ID aplikace na webu Azure Portal](./media/automate-with-logic-apps/appid.png) 

Zadejte název připojení, ID aplikace a klíč rozhraní API.

![Okno připojení toku návrhář aplikace logiky](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Krok 5: Zadejte typ dotazu a graf Analytics
V následujícím příkladu dotaz vybere neúspěšných žádostí za poslední den a koreluje s výjimky, ke kterým došlo v rámci operace. Analytics koreluje neúspěšné požadavky na základě identifikátoru operation_Id. Výsledky dotazu pak segmenty pomocí algoritmu autocluster. 

Když vytvoříte vlastní dotazy, ověřte, že fungují správně v Analytics předtím, než ho přidáte do vašeho toku.

1. V **dotazu** přidejte následující dotaz Analytics: 

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

1. V **typ grafu** vyberte **tabulku Html**.

    ![Okno Konfigurace dotazu Analytics](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Krok 6: Konfigurace aplikace logiky k odesílání e-mailu

1. Klikněte na tlačítko **nový krok**a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte **Office 365 Outlook**.

1. Klikněte na tlačítko **Office 365 Outlook – odeslat e-mail**.

    ![Výběr Office 365 Outlooku](./media/automate-with-logic-apps/flow2b.png)

1. V **odeslat e-mailu** okno, postupujte takto:

   a. Zadejte e-mailovou adresu příjemce.

   b. Zadejte předmět e-mailu.

   c. Klikněte kamkoli do **tělo** pole a pak v nabídce dynamického obsahu, které se otevře na pravé straně vyberte **tělo**.

   d. Klikněte na tlačítko **zobrazit pokročilé možnosti**.

      ![Konfigurace Office 365 Outlooku](./media/automate-with-logic-apps/flow5.png)

1. V nabídce dynamického obsahu postupujte takto:

    a. Vyberte **název přílohy**.

    b. Vyberte **obsah přílohy**.
    
    c. V **je HTML** vyberte **Ano**.

      ![Obrazovka Konfigurace e-mailu Office 365](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Krok 7: Uložit a otestujte aplikaci logiky
* Klikněte na tlačítko **Uložit** uložte provedené změny.

Můžete počkat, aktivační událost pro spuštění aplikace logiky nebo aplikace logiky můžete spustit okamžitě tak, že vyberete **spustit**.

![Vytvoření obrazovky v aplikaci logiky](./media/automate-with-logic-apps/step7.png)

Při spuštění aplikace logiky, příjemce, které jste zadali v seznamu e-mailu obdrží e-mailu, který vypadá takto:

![E-mailové zprávy aplikace logiky](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Další postup

- Další informace o vytváření [analytických dotazů](../log-analytics/query-language/get-started-queries.md).
- Další informace o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





