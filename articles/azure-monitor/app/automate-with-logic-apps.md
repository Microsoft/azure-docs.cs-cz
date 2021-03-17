---
title: Automatizace procesů Azure Application Insights pomocí Logic Apps
description: Přečtěte si, jak můžete rychle automatizovat opakované procesy tím, že do aplikace logiky přidáte konektor Application Insights.
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: d7ff75be3cb847235405a740df4a20803cdc87b3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589930"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatizace Application Insights procesů pomocí Logic Apps

Najdete v datech telemetrie opakované spouštění stejných dotazů, abyste zkontrolovali, jestli služba funguje správně? Chcete tyto dotazy automatizovat pro hledání trendů a anomálií a pak pro ně sestavovat vlastní pracovní postupy? Konektor služby Azure Application Insights pro Logic Apps je pravý Nástroj pro tento účel.

> [!NOTE]
> Konektor služby Azure Application Insights byl nahrazen [konektorem Azure monitor](../logs/logicapp-flow-connector.md) , který je integrovaný se Azure Active Directory místo vyžadování klíče rozhraní API a také umožňuje načíst data z Log Analytics pracovního prostoru.

Pomocí této integrace můžete automatizovat mnoho procesů bez nutnosti psát jediný řádek kódu. Pomocí konektoru Application Insights můžete vytvořit aplikaci logiky a rychle tak automatizovat všechny Application Insights procesy. 

Můžete také přidat další akce. Funkce Logic Apps v Azure App Service zpřístupňuje stovky akcí. Například pomocí aplikace logiky můžete automaticky odeslat e-mailové oznámení nebo vytvořit chybu v Azure DevOps. Můžete také použít jednu z mnoha dostupných [šablon](../../logic-apps/logic-apps-create-logic-apps-from-templates.md) , které vám pomůžou urychlit proces vytváření aplikace logiky. 

## <a name="create-a-logic-app-for-application-insights"></a>Vytvoření aplikace logiky pro Application Insights

V tomto kurzu se naučíte, jak vytvořit aplikaci logiky, která používá algoritmus autocluster Analytics k seskupení atributů v datech webové aplikace. Tok automaticky odesílá výsledky na základě e-mailu, stačí jenom jeden z příkladů, jak můžete Application Insights Analytics a Logic Apps společně používat. 

### <a name="step-1-create-a-logic-app"></a>Krok 1: Vytvoření aplikace logiky
1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Klikněte na **vytvořit prostředek**, vyberte **web a mobilní zařízení** a pak vyberte **Aplikace logiky**.

    ![Nové okno aplikace logiky](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Krok 2: vytvoření triggeru pro vaši aplikaci logiky
1. V okně **návrháře aplikace logiky** v části **začít se společným triggerem** vyberte **opakování**.

    ![Okno návrháře aplikace logiky](./media/automate-with-logic-apps/2logicappdesigner.png)

1. Do pole  **interval** zadejte **1** a pak,**počet_plateb** , vyberte **den**.

    ![Okno opakování návrháře aplikace logiky](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Krok 3: Přidání akce Application Insights
1. Klikněte na **Nový krok**.

1. Do vyhledávacího pole **zvolit akci** zadejte **Azure Application Insights**.

1. V části **Akce** klikněte na **analytické dotazy Azure Application Insights – vizualizace**.

    ![Návrhář aplikace logiky "Výběr akce"](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Krok 4: připojení k prostředku Application Insights

K dokončení tohoto kroku potřebujete ID aplikace a klíč rozhraní API pro váš prostředek. Můžete je načíst z Azure Portal, jak je znázorněno v následujícím diagramu:

![Snímek obrazovky s vybraným tlačítkem vytvořit klíč rozhraní API zobrazí na stránce Azure Portal přístup k rozhraní API.](./media/automate-with-logic-apps/5apiaccess.png)

![ID aplikace v Azure Portal](./media/automate-with-logic-apps/6apikey.png)

Zadejte název připojení, ID aplikace a klíč rozhraní API.

![Okno připojení toku návrháře aplikace logiky](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Krok 5: zadejte dotaz Analytics a typ grafu
V následujícím příkladu dotaz vybírá neúspěšné žádosti za poslední den a koreluje s výjimkami, k nimž došlo v rámci operace. Analýza koreluje neúspěšné požadavky na základě identifikátoru operation_Id. Dotaz pak segmentuje výsledky pomocí algoritmu autocluster. 

Když vytváříte vlastní dotazy, ověřte, že správně fungují v analytických prostředí, než je přidáte do vašeho toku.

1. Do pole **dotaz** přidejte následující analytický dotaz:

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

1. V poli **typ grafu** vyberte **tabulka HTML**.

    ![Okno Konfigurace dotazu Analytics](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Krok 6: Konfigurace aplikace logiky pro odesílání e-mailů

1. Klikněte na **Nový krok**.

1. Do vyhledávacího pole zadejte **Office 365 Outlook**.

1. Klikněte na **Office 365 Outlook – odeslat e-mail**.

    ![Výběr aplikace Office 365 Outlook](./media/automate-with-logic-apps/9sendemail.png)

1. V okně **Odeslat e-mail** udělejte toto:

   a. Zadejte e-mailovou adresu příjemce.

   b. Zadejte předmět e-mailu.

   c. Klikněte kamkoli do pole **text** a potom v nabídce dynamického obsahu, který se otevře vpravo, vyberte **text**.
    
   d. Klikněte na rozevírací seznam **Přidat nový parametr** a vyberte přílohy a je HTML.

      ![Snímek obrazovky se zvýrazněným oknem pro odeslání e-mailu s zvýrazněným polem tělo a nabídkou dynamického obsahu se zvýrazněným textem na pravé straně.](./media/automate-with-logic-apps/10emailbody.png)

      ![Konfigurace Office 365 Outlooku](./media/automate-with-logic-apps/11emailparameter.png)

1. V nabídce dynamický obsah udělejte toto:

    a. Vyberte **název přílohy**.

    b. Vyberte **obsah přílohy**.
    
    c. V poli **je HTML** vyberte **Ano**.

      ![Obrazovka konfigurace e-mailu Office 365](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Krok 7: uložení a otestování aplikace logiky
* Kliknutím na **Uložit** změny uložte.

Můžete počkat, až aktivační událost spustí aplikaci logiky, nebo můžete spustit aplikaci logiky hned po výběru možnosti **Spustit**.

![Obrazovka pro vytvoření aplikace logiky](./media/automate-with-logic-apps/13save.png)

Po spuštění aplikace logiky obdrží příjemci, které jste zadali v seznamu e-mailů, e-mail, který bude vypadat nějak takto:

![E-mailová zpráva aplikace logiky](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o vytváření [analytických dotazů](../logs/get-started-queries.md).
- Další informace o [Logic Apps](../../logic-apps/logic-apps-overview.md).



<!--Link references-->

