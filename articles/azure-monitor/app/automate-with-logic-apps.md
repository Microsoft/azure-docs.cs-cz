---
title: Automatizace procesů Azure Application Insights pomocí logic apps
description: Zjistěte, jak můžete rychle automatizovat opakovatelné procesy přidáním konektoru Application Insights do aplikace logiky.
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 4a0944e661932d86fac75f78c4faf5be751806c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473162"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatizace procesů Přehledů aplikací pomocí logic ových aplikací

Zjistíte, že opakovaně spouštějí stejné dotazy na telemetrická data ke kontrole, zda vaše služba funguje správně? Chcete tyto dotazy automatizovat pro hledání trendů a anomálií a pak kolem nich vytvářet vlastní pracovní postupy? Konektor Azure Application Insights pro logic apps je pro tento účel správným nástrojem.

> [!NOTE]
> Konektor Azure Application Insights byl nahrazen [konektorem Azure Monitor,](../platform/logicapp-flow-connector.md) který je integrovaný s Azure Active Directory namísto vyžadování klíče rozhraní API a také umožňuje načítat data z pracovního prostoru Log Analytics.

Díky této integraci můžete automatizovat mnoho procesů bez psaní jediného řádku kódu. Můžete vytvořit aplikaci logiky s konektorem Application Insights pro rychlé automatizaci jakéhokoli procesu Application Insights. 

Můžete také přidat další akce. Funkce Logic Apps služby Azure App Service zpřístupňuje stovky akcí. Například pomocí aplikace logiky můžete automaticky odeslat e-mailové oznámení nebo vytvořit chybu v Azure DevOps. Můžete také použít jednu z mnoha [dostupných šablon,](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) které vám pomohou urychlit proces vytváření aplikace logiky. 

## <a name="create-a-logic-app-for-application-insights"></a>Vytvoření aplikace logiky pro přehledy aplikací

V tomto kurzu se dozvíte, jak vytvořit aplikaci logiky, která používá algoritmus automatického clusteru Analytics k seskupení atributů v datech pro webovou aplikaci. Tok automaticky odešle výsledky e-mailem, jen jeden příklad toho, jak můžete společně používat Application Insights Analytics a Logic Apps. 

### <a name="step-1-create-a-logic-app"></a>Krok 1: Vytvoření aplikace logiky
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Klepněte na tlačítko **Vytvořit prostředek**, vyberte **možnost Web + Mobilní**a pak vyberte **položku Aplikace logiky**.

    ![Okno nové aplikace logiky](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Krok 2: Vytvoření aktivační události pro aplikaci logiky
1. V okně **Návrhář aplikace logiky** vyberte v části **Start s běžnou aktivační událostí**možnost **Opakování**.

    ![Okno Návrhář epo-aplikace logiky](./media/automate-with-logic-apps/2logicappdesigner.png)

1. Do pole **Interval** zadejte **1** a potom vpoli**Frekvence** vyberte **Den**.

    ![Okno Návrháře aplikace logiky "Opakování"](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Krok 3: Přidání akce Přehledy aplikací
1. Klepněte na **nový krok**.

1. Do pole Zvolit hledání **akcí** zadejte **Azure Application Insights**.

1. V části **Akce**klikněte na **Azure Application Insights – Visualize Analytics query**.

    ![Okno Návrhář aplikace logiky "Zvolit akci" okno](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Krok 4: Připojení k prostředku Application Insights

K dokončení tohoto kroku potřebujete ID aplikace a klíč rozhraní API pro váš prostředek. Můžete je načíst z portálu Azure, jak je znázorněno na následujícím diagramu:

![ID aplikace na webu Azure Portal](./media/automate-with-logic-apps/5apiaccess.png)

![ID aplikace na webu Azure Portal](./media/automate-with-logic-apps/6apikey.png)

Zadejte název připojení, ID aplikace a klíč rozhraní API.

![Okno připojení toku aplikace Logika](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Krok 5: Určení dotazu analytics a typu grafu
V následujícím příkladu dotaz vybere neúspěšné požadavky během posledního dne a koreluje je s výjimkami, ke kterým došlo v rámci operace. Služba Analytics koreluje neúspěšné požadavky na základě identifikátoru operation_Id. Dotaz pak segmenty výsledky pomocí algoritmu autocluster. 

Při vytváření vlastních dotazů před přidáním do toku ověřte, zda v Analytics fungují správně.

1. Do pole **Dotaz** přidejte následující dotaz Analytics:

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

1. V poli **Typ grafu** vyberte **Tabulka HTML**.

    ![Okno konfigurace dotazu Analytics](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Krok 6: Konfigurace aplikace logiky pro odesílání e-mailů

1. Klepněte na **nový krok**.

1. Do vyhledávacího pole zadejte **Office 365 Outlook**.

1. Klikněte na **Office 365 Outlook – Poslat e-mail**.

    ![Výběr Office 365 Outlooku](./media/automate-with-logic-apps/9sendemail.png)

1. V okně **Odeslat e-mail** postupujte takto:

   a. Zadejte e-mailovou adresu příjemce.

   b. Zadejte předmět e-mailu.

   c. Klepněte na libovolné místo v poli **Tělo** a v nabídce dynamického obsahu, která se otevře vpravo, vyberte **body**.
    
   d. Klikněte na rozevírací seznam **Přidat nový parametr** a vyberte Přílohy a Je HTML.

      ![Konfigurace Office 365 Outlooku](./media/automate-with-logic-apps/10emailbody.png)

      ![Konfigurace Office 365 Outlooku](./media/automate-with-logic-apps/11emailparameter.png)

1. V nabídce dynamického obsahu postupujte takto:

    a. Vyberte **název přílohy**.

    b. Vyberte **Obsah přílohy**.
    
    c. V poli **Je HTML** vyberte **Ano**.

      ![Obrazovka konfigurace e-mailu Office 365](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Krok 7: Uložení a testování aplikace logiky
* Kliknutím na **Uložit** uložte změny.

Můžete počkat na aktivační událost ke spuštění aplikace logiky, nebo můžete spustit aplikaci logiky okamžitě výběrem **spustit**.

![Obrazovka pro vytváření aplikace logiky](./media/automate-with-logic-apps/13save.png)

Při spuštění aplikace logiky příjemci, které jste zadali v seznamu e-mailů, obdrží e-mail, který vypadá takto:

![E-mailová zpráva aplikace Logika](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o vytváření [dotazů analytics](../../azure-monitor/log-query/get-started-queries.md).
- Další informace o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





