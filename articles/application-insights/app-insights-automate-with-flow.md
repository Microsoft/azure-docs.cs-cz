---
title: Automatizovat procesy Azure Application Insights s Flow Microsoft
description: Zjistěte, jak Microsoft Flow můžete rychle automatizovat opakované procesy pomocí konektoru služby Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2017
ms.author: mbullwin
ms.openlocfilehash: c92b50ee78c100a531a2973181251444b3810967
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293766"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizovat procesy Azure Application Insights s konektorem pro Flow Microsoft

Se přistihnete opakovaně spuštění stejné dotazů na data telemetrie zkontrolujte, zda je vaše služba funguje správně? Hledáte automatizovat tyto dotazy pro hledání trendů a anomálií, a následně vytvořit vlastní pracovní postupy je obcházet? Konektor služby Azure Application Insights (preview) pro Microsoft Flow je ten nejvhodnější nástroj pro tyto účely.

Díky této integraci teď procesy můžete automatizovat množství bez nutnosti napsat jediný řádek kódu. Po vytvoření tokem pomocí akce Application Insights, tok automaticky spustí dotaz Application Insights Analytics. 

Můžete přidat i další akce. Microsoft Flow zpřístupní stovky akce. Například můžete Flow Microsoft automaticky odesílat e-mailových oznámení nebo vytvoření chyby ve Visual Studio Team Services. Můžete také použít jednu z dalších [šablony](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) které jsou k dispozici pro konektor pro Flow společnosti Microsoft. Tyto šablony urychlit proces vytváření k toku. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Vytvoření postup pro službu Application Insights

V tomto kurzu se dozvíte, jak vytvořit toku, který používá algoritmus automatického clusteru Analytics skupiny atributů v datech pro webovou aplikaci. Tok automaticky odesílá výsledky e-mailem, pouze příklad, jak můžete používat Microsoft Flow a analýza Statistika aplikace společně. 

### <a name="step-1-create-a-flow"></a>Krok 1: Vytvoření toku
1. Přihlaste se k [Microsoft Flow](http://flow.microsoft.com)a potom vyberte **Moje toků**.
2. Klikněte na tlačítko **vytvořit tokem z prázdné**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Krok 2: Vytvoření aktivační událost pro vaše tok
1. Vyberte **plán**a potom vyberte **plán - opakování**.
2. V **frekvence** vyberte **den**a v **Interval** zadejte **1**.

    ![Dialogové okno Microsoft Flow aktivační události](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>Krok 3: Přidání Application Insights akce
1. Klikněte na tlačítko **nový krok**a potom klikněte na **přidat akci**.
2. Vyhledejte **Azure Application Insights**.
3. Klikněte na tlačítko **Azure Application Insights – vizualizovat analýzy dotaz Preview**.

    ![Spusťte okno dotazu Analytics](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Krok 4: Připojení k prostředku Application Insights

K dokončení tohoto kroku, musíte aplikaci ID a klíč rozhraní API pro prostředek. Můžete je znovu načíst z portálu Azure, jak je znázorněno v následujícím diagramu:

![ID aplikace v portálu Azure](./media/app-insights-automate-with-flow/appid.png) 

- Zadejte název připojení, spolu s klíč rozhraní API a ID aplikace.

    ![Okno Microsoft Flow připojení](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Krok 5: Zadejte typ dotazu a graf analýzy
Tento příklad dotaz vybere neúspěšných požadavků v rámci poslední den a jejich koreluje s výjimkami, k nimž došlo v rámci operace. Analýza korelaci je na základě identifikátoru operation_Id. Výsledky dotazu pak segmenty pomocí algoritmu autocluster. 

Když vytvoříte vlastní dotazy, ověřte, že fungují správně v Analytics předtím, než ho přidáte do vašeho toku.

- Přidejte následující dotaz analýzy a pak vyberte typ grafu tabulky HTML. 

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
    
    ![Okno Konfigurace analýzy dotazu](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Krok 6: Konfigurace toku k odeslání e-mailu

1. Klikněte na tlačítko **nový krok**a potom klikněte na **přidat akci**.
2. Vyhledejte **Office 365 Outlook**.
3. Klikněte na tlačítko **Office 365 Outlook – e-mailu**.

    ![Okno Výběr Outlook Office 365](./media/app-insights-automate-with-flow/flow2b.png)

4. V **e-mailovou zprávu** okno, postupujte takto:

   a. Zadejte e-mailová adresa příjemce.

   b. Zadejte předmět e-mailu.

   c. Klikněte kamkoli do **textu** pole a pak na dynamické kontextové nabídky, které se otevře napravo, vyberte **textu**.

   d. Klikněte na tlačítko **zobrazit rozšířené možnosti**.

    ![Konfigurace aplikace Outlook Office 365](./media/app-insights-automate-with-flow/flow5.png)

5. V nabídce dynamického obsahu postupujte takto:

    a. Vyberte **název přílohy**.

    b. Vyberte **obsah přílohy**.
    
    c. V **je HTML** vyberte **Ano**.

    ![Okno Konfigurace e-mailu Office 365](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>Krok 7: Uložit a testování vaší toku
- V **toku název** pole, přidejte název vaší toku a pak klikněte na tlačítko **vytvořit toku**.

    ![Postup vytvoření okna](./media/app-insights-automate-with-flow/flow8.png)

Počkejte, spouštějí tuto akci, nebo můžete spustit toku okamžitě nástrojem [spuštění aktivační události na vyžádání](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Při spuštění toku, příjemce, které jste zadali v seznamu e-mailu dostávat e-mailovou zprávu, která vypadá takto:

![Ukázkového e-mailu](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Další postup

- Další informace o vytváření [analytické dotazy](app-insights-analytics-using.md).
- Další informace o [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





