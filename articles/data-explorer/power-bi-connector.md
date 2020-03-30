---
title: Vizualizace dat pomocí konektoru Azure Data Explorer pro Power BI
description: 'V tomto článku se dozvíte, jak použít jednu ze tří možností vizualizace dat v Power BI: konektor U Power BI pro Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a95d45481bed17e46429e3a22dff4b8cc62354a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560486"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Vizualizace dat pomocí konektoru Azure Data Explorer pro Power BI

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Power BI je řešení obchodní analýzy, které umožňuje vizualizovat data a sdílet výsledky v rámci organizace. Azure Data Explorer nabízí tři možnosti pro připojení k datům v Power BI: použít integrovaný konektor, importovat dotaz z Azure Data Exploreru nebo použít dotaz SQL. V tomto článku se ukazuje, jak pomocí integrovaného konektoru získat data a vizualizovat je v sestavě Power BI. Použití nativního konektoru Azure Data Explorer pro vytváření řídicích panelů Power BI je jednoduché. Konektor Power BI podporuje [režimy připojení importu a přímého dotazu](https://docs.microsoft.com/power-bi/desktop-directquery-about). Řídicí panely můžete vytvářet pomocí **režimu importu** nebo **directquery** v závislosti na scénáři, škálování a požadavcích na výkon. 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku je třeba provést následující:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* E-mailový účet organizace, který je členem adresáře Azure Active Directory, takže se můžete připojit k [clusteru nápovědy Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples).
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (zvolte **STÁHNOUT ZDARMA)**

## <a name="get-data-from-azure-data-explorer"></a>Získání dat z Průzkumníka dat Azure

Nejprve se připojíte ke clusteru nápovědy Azure Data Explorer, pak přenést podmnožinu dat z tabulky *StormEvents.* [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. V Power BI Desktopu na kartě **Domů** vyberte **Získat data** a pak **Další**.

    ![Získání dat](media/power-bi-connector/get-data-more.png)

1. Vyhledejte *Azure Data Explorer*, vyberte Azure Data **Explorer** a pak **Connect**.

    ![Vyhledání a získání dat](media/power-bi-connector/search-get-data.png)

1. Na obrazovce **Průzkumníka dat Azure (Kusto)** vyplňte formulář s následujícími informacemi.

    ![Možnosti clusteru, databáze a tabulky](media/power-bi-connector/cluster-database-table.png)

    **Nastavení** | **Hodnotu** | **Popis pole**
    |---|---|---|
    | Cluster | *https://help.kusto.windows.net* | Adresa URL clusteru nápovědy. U jiných clusterů je adresa URL ve formuláři *https://\<Název_clusteru\>.\< Oblast\>.kusto.windows.net*. |
    | Databáze | Ponechte prázdné | Databáze, která je hostována v clusteru, ke kterému se připojujete. Tuto možnost vybereme v pozdějším kroku. |
    | Název tabulky | Ponechte prázdné | Jedna z tabulek v databázi <code>StormEvents \| take 1000</code>nebo dotaz jako . Tuto možnost vybereme v pozdějším kroku. |
    | Upřesnit možnosti | Ponechte prázdné | Možnosti pro vaše dotazy, například velikost sady výsledků. |
    | Režim datového připojení | *DirectQuery* | Určuje, jestli Power BI importuje data nebo se připojí přímo ke zdroji dat. S tímto konektorem můžete použít obě možnosti. |
    | | | |
    
    > [!NOTE]
    > V režimu **importu** se data přesunou do Power BI. V režimu **DirectQuery** se data dotazují přímo z clusteru Průzkumníka dat Azure.
    >
    > Režim **importu** použijte v:
    > * Vaše datová sada je malá.
    > * Nepotřebujete téměř data v reálném čase. 
    > * Vaše data jsou již agregována nebo provádíte [agregaci v Kustu](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions)    
    >
    > Režim **DirectQuery** použijte v:
    > * Vaše datová sada je velmi velká. 
    > * Potřebujete data téměř v reálném čase.   

1. Pokud ještě nemáte připojení ke clusteru nápovědy, přihlaste se. Přihlaste se pomocí účtu organizace a pak vyberte **Připojit**.

    ![Přihlášení](media/power-bi-connector/sign-in.png)

1. Na obrazovce **Navigátor** rozbalte databázi **Ukázky** a vyberte **StormEvents** a pak **Upravit**.

    ![Vybrat tabulku](media/power-bi-connector/select-table.png)

    Tabulka se otevře v editoru Power Query, kde můžete před importem dat upravit řádky a sloupce.

1. V Editoru Power Query vyberte šipku vedle sloupce **DamageCrops** a **seřadit**.

    ![Seřadit DamageCrops sestupně](media/power-bi-connector/sort-descending.png)

1. Na kartě **Domů** vyberte **Zachovat řádky** a pak Zachovat **horní řádky**. Zadejte hodnotu *1000,* aby se do horních 1000 řádků seřazené tabulky.

    ![Zachovat horní řádky](media/power-bi-connector/keep-top-rows.png)

1. Na kartě **Domů** vyberte **Zavřít & Použít**.

    ![Zavřít a použít](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Vizualizace dat v sestavě

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete sestavu, kterou jste pro tento článek vytvořili, odstraňte soubor Power BI Desktop (.pbix).

## <a name="next-steps"></a>Další kroky

[Tipy pro použití konektoru Azure Data Explorer pro Power BI k dotazování dat](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
