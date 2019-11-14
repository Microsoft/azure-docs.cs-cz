---
title: Vizualizace dat pomocí konektoru služby Azure Průzkumník dat pro Power BI
description: 'V tomto článku se dozvíte, jak použít jednu ze tří možností pro vizualizaci dat v Power BI: konektor Power BI pro Azure Průzkumník dat.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 361ea6ed76207e8e9721f64df61738b6cd9631dc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024217"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Vizualizace dat pomocí konektoru služby Azure Průzkumník dat pro Power BI

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Power BI je řešení obchodní analýzy, které umožňuje vizualizovat data a sdílet výsledky v rámci organizace. Azure Průzkumník dat poskytuje tři možnosti pro připojení k datům v Power BI: použijte integrovaný konektor, importujte dotaz z Azure Průzkumník dat nebo použijte dotaz SQL. V tomto článku se dozvíte, jak pomocí integrovaného konektoru získat data a vizualizovat je v sestavě Power BI. Použití Azure Průzkumník dat Native Connector pro vytváření Power BIch řídicích panelů je jednoduché. Konektor Power BI podporuje [režimy importu a přímých připojení dotazů](https://docs.microsoft.com/power-bi/desktop-directquery-about). Řídicí panely můžete sestavit pomocí režimu **importu** nebo **DirectQuery** v závislosti na scénářích, škálování a požadavcích na výkon. 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete následující:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* E-mailový účet organizace, který je členem Azure Active Directory, abyste se mohli připojit ke [clusteru azure Průzkumník dat Help](https://dataexplorer.azure.com/clusters/help/databases/samples).
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (vyberte **Stáhnout zdarma**)

## <a name="get-data-from-azure-data-explorer"></a>Získat data z Azure Průzkumník dat

Nejprve se připojíte ke clusteru Azure Průzkumník dat Help a pak přenesete podmnožinu dat z tabulky *StormEvents* . [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. V Power BI Desktop na kartě **Domů** vyberte **získat data** a pak **Další**.

    ![Získání dat](media/power-bi-connector/get-data-more.png)

1. Vyhledejte *azure Průzkumník dat*, vyberte **Azure Průzkumník dat** pak se **Připojte**.

    ![Vyhledání a získání dat](media/power-bi-connector/search-get-data.png)

1. Na obrazovce **Azure Průzkumník dat (Kusto)** vyplňte formulář následujícími informacemi.

    ![Možnosti clusteru, databáze a tabulky](media/power-bi-connector/cluster-database-table.png)

    **Nastavení** | **Hodnota** | **Popis pole**
    |---|---|---|
    | Cluster | *https://help.kusto.windows.net* | Adresa URL pro cluster s nápovědu Pro jiné clustery je adresa URL ve tvaru *https://\<\>název_clusteru.\<oblasti\>. kusto.Windows.NET*. |
    | Databáze | Ponechte prázdné | Databáze, která je hostována v clusteru, ke kterému se připojujete. Tuto možnost vybereme v pozdějším kroku. |
    | Název tabulky | Ponechte prázdné | Jedna z tabulek v databázi nebo dotaz, jako je například <code>StormEvents \| take 1000</code>. Tuto možnost vybereme v pozdějším kroku. |
    | Rozšířené možnosti | Ponechte prázdné | Možnosti pro vaše dotazy, jako je například velikost sady výsledků. |
    | Režim připojení dat | *DirectQuery* | Určuje, zda Power BI importuje data nebo se připojí přímo ke zdroji dat. V této spojnici můžete použít jednu z možností. |
    | | | |
    
    > [!NOTE]
    > V režimu **importu** se data přesunou do Power BI. V režimu **DirectQuery** se data dotazují přímo z vašeho clusteru Azure Průzkumník dat.
    >
    > Režim **importu** použijte v těchto případech:
    > * Vaše datová sada je malá.
    > * Nepotřebujete data téměř v reálném čase. 
    > * Vaše data jsou už agregovaná nebo se [v Kusto provádí agregace](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions) .    
    >
    > Režim **DirectQuery** použijte v těchto případech:
    > * Vaše datová sada je velmi velká. 
    > * Potřebujete data téměř v reálném čase.   

1. Pokud ještě nemáte připojení ke clusteru podpory, přihlaste se. Přihlaste se pomocí účtu organizace a pak vyberte **připojit**.

    ![Přihlášení](media/power-bi-connector/sign-in.png)

1. Na obrazovce **navigátor** rozbalte databázi **Samples** , vyberte **StormEvents** a pak **Upravit**.

    ![vybrat tabulku](media/power-bi-connector/select-table.png)

    Tabulka se otevře v editoru Power Query, kde můžete před importem dat upravit řádky a sloupce.

1. V editoru Power Query vyberte šipku vedle sloupce **DamageCrops** a pak **seřaďte sestupné řazení**.

    ![Seřadit DamageCrops sestupně](media/power-bi-connector/sort-descending.png)

1. Na kartě **Domů** vyberte možnost **ponechat řádky** a potom **zachovejte horní řádky**. Zadejte hodnotu *1000* , která se má přenést do prvních 1000 řádků seřazené tabulky.

    ![Zachovat horní řádky](media/power-bi-connector/keep-top-rows.png)

1. Na kartě **Domů** vyberte **Zavřít & použít**.

    ![Zavřít a použít](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Vizualizace dat v sestavě

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete sestavu, kterou jste vytvořili pro tento článek, odstraňte soubor Power BI Desktop (. pbix).

## <a name="next-steps"></a>Další kroky

[Tipy pro použití konektoru služby Azure Průzkumník dat pro Power BI k dotazování na data](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
