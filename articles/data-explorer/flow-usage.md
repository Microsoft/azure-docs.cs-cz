---
title: Příklady využití konektoru toku microsoft azure datového průzkumníka (Preview)
description: Seznamte se s některými běžnými příklady použití konektoru Microsoft Flow.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 796b37f98fed7e389fa71a15b5e6697a14db1a16
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397188"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Příklady použití konektoru Microsoft Flow (Preview)

Konektor toku Průzkumníka dat Azure umožňuje Průzkumníku dat Azure používat [funkce Toku Microsoft Power Automate](https://flow.microsoft.com/) k automatickému spouštění dotazů a příkazů Kusto jako součásti naplánované nebo aktivované úlohy. Tento dokument obsahuje několik běžných příkladů použití konektoru Microsoft Flow.

Další informace naleznete v [tématu Microsoft Flow konektor (Preview)](flow.md).

* [Konektor Microsoft Flow a SQL](#microsoft-flow-connector-and-sql)
* [Nabízená data do datové sady Power BI](#push-data-to-power-bi-dataset)
* [Podmíněné dotazy](#conditional-queries)
* [Odeslání e-mailu s více grafy toku Průzkumníka dat Azure](#email-multiple-azure-data-explorer-flow-charts)
* [Odeslání jiného e-mailu různým kontaktům](#send-a-different-email-to-different-contacts)
* [Vytvoření vlastní tabulky HTML](#create-a-custom-html-table)

## <a name="microsoft-flow-connector-and-sql"></a>Konektor Microsoft Flow a SQL

Pomocí konektoru Microsoft Flow můžete dotazovat data a agregovat je v databázi SQL.

> [!Note]
> SQL insert se provádí samostatně pro každý řádek. Konektor Microsoft Flow používejte pouze pro malá množství výstupních dat. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> Do pole *Název clusteru* zadejte adresu URL clusteru.

## <a name="push-data-to-power-bi-dataset"></a>Nabízená data do datové sady Power BI

Konektor Microsoft Flow lze použít společně s konektorem Power BI k nabízení dat z dotazů Kusto do datových sad streamování Power BI.

1. Vytvořte novou akci Spustit dotaz a seznam výsledků.
1. Vyberte **Nový krok**.
1. Vyberte **Přidat akci** a vyhledejte Power BI.
1. Vyberte **Power BI**.
1. Vyberte **Přidat řádky do datové sady**. 

    ![Konektor Flow Power BI](./media/flow-usage/flow-powerbiconnector.png)
1. Zadejte **pracovní prostor**, **datovou sadu**a **tabulku,** do které budou data nabízena.
1. V dialogovém okně dynamického obsahu přidejte datovou část obsahující schéma datové sady a příslušné výsledky dotazu Kusto.

    ![Pole Power BI toku](./media/flow-usage/flow-powerbifields.png)

Tok automaticky použije akci Power BI pro každý řádek tabulky výsledků dotazu Kusto. 

![Akce Tok Power BI pro každý řádek](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Podmíněné dotazy

Výsledky dotazů Kusto lze použít jako vstup nebo podmínky pro další akce toku.

V následujícím příkladu se dotazujeme Kusto na incidenty, ke kterým došlo během posledního dne. Pro každý vyřešený incident je zaúčtována zpráva slack a vytvoří se nabízené oznámení.
Pro každý incident, který je stále aktivní, kusto je dotazován na další informace o podobných incidentů. Odešle tyto informace jako e-mail a otevře související úkol TFS.

Podle těchto pokynů vytvořte podobný tok:

1. Vytvořte novou akci Spustit dotaz a seznam výsledků.
1. Vyberte **Nový krok**.
1. Vyberte **ovládací prvek Podmínka**.
1. V okně dynamického obsahu vyberte parametr, který chcete použít jako podmínku pro další akce.
1. Vyberte typ *relace* a *hodnoty,* chcete-li nastavit konkrétní podmínku pro daný parametr.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Tok použije tuto podmínku na každý řádek tabulky výsledků dotazu.
1. Přidejte akce, pokud je podmínka pravdivá a nepravdivá.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

Hodnoty výsledků z dotazu Kusto můžete použít jako vstup pro další akce. Vyberte hodnoty výsledků z okna dynamického obsahu.
V níže uvedeném příkladu byla přidána akce Slack – post message a Visual Studio – vytvoření nové akce pracovní položky obsahující data z dotazu Kusto.

![Časová rezerva – akce Odeslat zprávu](./media/flow-usage/flow-slack.png)

![Akce visual studia](./media/flow-usage/flow-visualstudio.png)

V tomto příkladu pokud incident je stále aktivní, dotaz Kusto znovu získat informace o tom, jak byly vyřešeny incidenty ze stejného zdroje v minulosti.

![Dotaz na podmínku toku](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> Do pole *Název clusteru* zadejte adresu URL clusteru.

Vizualizujte tyto informace jako výsečový graf a pošlete je e-mailem týmu.

![E-mail s podmínkou toku](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Odeslání e-mailu s více grafy toku Průzkumníka dat Azure

1. Vytvořte nový tok s aktivační událostí "Opakování" a definujte interval toku a frekvenci. 
1. Přidejte nový krok s jednou nebo více akcemi Kusto - Run query a vizualizovat výsledky. 

    ![Spuštění několika dotazů v toku](./media/flow-usage/flow-severalqueries.png)
1. Pro každý Kusto - Spustit dotaz a vizualizovat výsledek definujte následující pole:
    * Adresa URL clusteru (v poli *Název clusteru)*
    * Název databáze
    * Typ dotazu a grafu (tabulka HTML/ výsečový graf/ časový graf/ pruhový graf/ Zadat vlastní hodnotu).

    ![Vizualizace výsledků s více přílohami](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

    > [!IMPORTANT]
    > Do polí *Název clusteru* zadejte adresu URL clusteru.

1. Přidejte akci Odeslat e-mail. 
    * Do pole *Tělo* vložte požadované tělo tak, aby vizualizovaný výsledek dotazu byl zahrnut do těla e-mailu.
    * Pokud chcete k e-mailu přidat přílohu, přidejte název přílohy a obsah přílohy.
    
    ![Odeslání více příloh e-mailem](./media/flow-usage/flow-email-multiple-attachments.png)

    Úplné pokyny k vytvoření akce e-mailu najdete v [tématu E-mail s výsledky dotazu aplikace Kusto](flow.md#email-kusto-query-results). 

Výsledky:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="send-a-different-email-to-different-contacts"></a>Odeslání jiného e-mailu různým kontaktům

Microsoft Flow můžete využít k odesílání různých přizpůsobených e-mailů různým kontaktům. E-mailové adresy a obsah e-mailu jsou výsledkem dotazu Kusto.

Příklad:

![Dynamický e-mail pomocí dotazu Kusto](./media/flow-usage/flow-dynamicemailkusto.png)

> [!IMPORTANT]
> Do pole *Název clusteru* zadejte adresu URL clusteru.

![Dynamický e-mail v akci toku](./media/flow-usage/flow-dynamicemail.png)

## <a name="create-a-custom-html-table"></a>Vytvoření vlastní tabulky HTML

Microsoft Flow můžete využít k vytváření a používání vlastních prvků HTML, jako je například vlastní tabulka HTML.

Následující příklad ukazuje, jak vytvořit vlastní tabulku HTML. Tabulka HTML bude mít své řádky barevné podle úrovně protokolu (stejně jako v Průzkumníku dat Azure).

Podle těchto pokynů vytvořte podobný tok:

1. Vytvořte novou akci Kusto - Spustit dotaz a seznam výsledků.

    ![Seznam výsledků pro tabulku HTML](./media/flow-usage/flow-listresultforhtmltable.png)

> [!IMPORTANT]
> Do pole *Název clusteru* zadejte adresu URL clusteru.

1. Smyčku přes výsledky dotazu a vytvořit html tabulkový text: 
    1. Chcete-li vytvořit proměnnou pro uložení řetězce HTML, vyberte **Nový krok**
    1. Vyberte **Přidat akci** a vyhledejte proměnné. 
    1. Vybrat **proměnné – inicializovat proměnnou**. 
    1. Inicializovat proměnnou řetězce takto:

    ![Inicializovat proměnnou](./media/flow-usage/flow-initializevariable.png)

1. Smyčka přes výsledky:
    1. Vyberte **Nový krok**.
    1. Vyberte **Přidat akci**.
    1. Vyhledejte proměnné. 
    1. Vyberte **proměnné - připojit k řetězci proměnné**. 
    1. Vyberte název proměnné, který jste inicializovali dříve, a vytvořte řádky tabulky HTML pomocí výsledků dotazu. 
    Při výběru výsledků dotazu se automaticky přidá použít pro každý z nich.

    V níže uvedeném `if` příkladu se výraz používá k definování stylu každého řádku:

    ```if(equals(items('Apply_to_each')?['Level'], 'Warning'), 'Yellow', if(equals(items('Apply_to_each')?['Level'], 'Error'), 'red', 'white'))```

    [![](./media/flow-usage/flow-createhtmltableloopcontent.png "Create HTML table loop content")](./media/flow-usage/flow-createhtmltableloopcontent.png#lightbox)

1. Vytvořte úplný obsah HTML: 
    1. Přidat novou akci mimo Použít pro každou z nich. 
    V následujícím příkladu je použitá akce Odeslat e-mail.
    1. Definujte tabulku HTML pomocí proměnné z předchozích kroků. 
    1. Pokud posíláte e-mail, vyberte **Zobrazit upřesňující možnosti** a v části Je HTML vyberte **Ano**.

    ![E-mail s vlastní tabulkou HTML](./media/flow-usage/flow-customhtmltablemail.png)

Result:

![Výsledek e-mailu vlastní tabulky HTML](./media/flow-usage/flow-customhtmltableresult.png)

## <a name="next-steps"></a>Další kroky

Další informace o [konektoru aplikace Microsoft Azure Explorer Logic App,](https://docs.microsoft.com/azure/kusto/tools/logicapps) což je další způsob, jak spustit kusto dotazy a příkazy automaticky jako součást naplánované nebo aktivované úlohy.
