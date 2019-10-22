---
title: Automatizace procesů Azure Application Insights s využitím Microsoft Flow
description: Naučte se, jak můžete použít Microsoft Flow k rychlé automatizaci opakujících se procesů pomocí konektoru Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/29/2019
ms.openlocfilehash: ff0896498c0270b8eb43b762228916985f924def
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678326"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizace procesů Azure Application Insights pomocí konektoru pro Microsoft Flow

Najdete v datech telemetrie opakované spouštění stejných dotazů, abyste zkontrolovali, jestli služba funguje správně? Chcete tyto dotazy automatizovat pro hledání trendů a anomálií a pak pro ně sestavovat vlastní pracovní postupy? Konektor služby Azure Application Insights pro Microsoft Flow je pravý Nástroj pro tyto účely.

Díky této integraci teď můžete automatizovat spoustu procesů bez nutnosti psát jediný řádek kódu. Když vytvoříte tok pomocí akce Application Insights, tok automaticky spustí dotaz Application Insights Analytics.

Můžete také přidat další akce. Microsoft Flow zpřístupňuje stovky dostupných akcí. Pomocí Microsoft Flow můžete například automaticky odeslat e-mailové oznámení nebo vytvořit chybu v Azure DevOps. Můžete také použít jednu z mnoha [šablon](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights) , které jsou k dispozici pro konektor pro Microsoft Flow. Tyto šablony urychlují proces vytváření toku.

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>Vytvoření toku pro Application Insights

V tomto kurzu se naučíte, jak vytvořit tok, který používá algoritmus analýzy autoclusteru k seskupení atributů v datech webové aplikace. Tok automaticky odesílá výsledky na základě e-mailu, stačí jenom jeden z příkladů, jak můžete používat Microsoft Flow a Application Insights analýzy společně.

### <a name="step-1-create-a-flow"></a>Krok 1: vytvoření toku

1. Přihlaste se k [Microsoft Flow](https://flow.microsoft.com)a pak vyberte **Moje toky**.
2. Klikněte na **Nový** a pak na **naplánované – od prázdné**.

    ![Vytvořit nový tok z naplánovaného prázdného](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Krok 2: vytvoření triggeru pro váš tok

1. V místní nabídce **Sestavte naplánovaný tok**, vyplňte název svého toku a to, jak často se má tok spouštět.

    ![Nastavte opakování plánu zadáním četnosti a intervalu.](./media/automate-with-flow/2-schedule.png)

1. Klikněte na **Vytvořit**.

### <a name="step-3-add-an-application-insights-action"></a>Krok 3: Přidání akce Application Insights

1. Vyhledejte **Application Insights**.
2. Klikněte na **Azure Application Insights – vizualizace dotazu Analytics**.

    ![Vyberte akci: dotaz Analytics pro Azure Application Insights vizualizuje](./media/automate-with-flow/3-visualize.png)

3. Vyberte **Nový krok**.

### <a name="step-4-connect-to-an-application-insights-resource"></a>Krok 4: připojení k prostředku Application Insights

K dokončení tohoto kroku potřebujete ID aplikace a klíč rozhraní API pro váš prostředek. Můžete je načíst z Azure Portal, jak je znázorněno v následujícím diagramu:

![ID aplikace v Azure Portal](./media/automate-with-flow/5apiaccess.png)

![Klíč rozhraní API v Azure Portal](./media/automate-with-flow/6apikey.png)

Zadejte název připojení spolu s ID aplikace a klíčem rozhraní API.

   ![Okno Microsoft Flow připojení](./media/automate-with-flow/4-connection.png)

Pokud se okno připojení nezobrazí hned a místo toho bude přímý dotaz na zadání dotazu, klikněte na tlačítko se třemi tečkami vpravo nahoře v poli. Pak vyberte moje připojení nebo použijte existující.

Klikněte na **Vytvořit**.

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Krok 5: zadejte dotaz Analytics a typ grafu
Tento ukázkový dotaz vybírá neúspěšné žádosti za poslední den a koreluje s výjimkami, k nimž došlo v rámci operace. Analýza je koreluje na základě identifikátoru operation_Id. Dotaz pak segmentuje výsledky pomocí algoritmu autocluster.

Když vytváříte vlastní dotazy, ověřte, že správně fungují v analytických prostředí, než je přidáte do vašeho toku.

- Přidejte následující analytický dotaz a vyberte typ grafu tabulky HTML. Pak vyberte **Nový krok**.

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
    
    ![Okno Konfigurace dotazu Analytics](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Krok 6: Konfigurace toku pro odesílání e-mailů

1. Vyhledejte **Office 365 Outlook**.
2. Klikněte na **Office 365 Outlook – odeslat e-mail**.

    ![Okno pro výběr aplikace Office 365 Outlook](./media/automate-with-flow/6-outlook.png)

1. V okně **Odeslat e-mail** :

   a. Zadejte e-mailovou adresu příjemce.

   b. Zadejte předmět e-mailu.

   c. Klikněte kamkoli do pole **text** a potom v nabídce dynamického obsahu, který se otevře vpravo, vyberte **text**.

   e. Vyberte **Zobrazit upřesňující možnosti** .

1. V nabídce dynamického obsahu:

    a. Vyberte **název přílohy**.

    b. Vyberte **obsah přílohy**.
    
    c. V poli **je HTML** vyberte **Ano**.

    ![Konfigurace Office 365 Outlooku](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>Krok 7: úspora a testování toku

Klikněte na **Uložit**.

Můžete počkat, až aktivační událost spustí tuto akci, nebo můžete kliknout na ![beaker ikonu testu ](./media/automate-with-flow/testicon.png) **test** v horní části.

Po výběru **testu**:

1. Vyberte možnost **provedem akci aktivace**.
2. Vyberte **Spustit tok**.

Po spuštění toku obdrží příjemci, které jste zadali v seznamu e-mailů, e-mailovou zprávu podobnou následující.

![Ukázkový e-mail](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o vytváření [analytických dotazů](../../azure-monitor/log-query/get-started-queries.md).
- Přečtěte si další informace o [Microsoft Flow](https://ms.flow.microsoft.com).

<!--Link references-->