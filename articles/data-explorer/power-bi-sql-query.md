---
title: Vizualizace dat z Azure Průzkumník dat pomocí dotazu SQL v Power BI
description: 'V tomto článku se dozvíte, jak použít jednu ze tří možností pro vizualizaci dat v Power BI: dotaz SQL na clusteru Azure Průzkumník dat.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: e4e7858a54f3002a511269a2519135d5ac24ed68
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024087"
---
# <a name="visualize-data-from-azure-data-explorer-using-a-sql-query-in-power-bi"></a>Vizualizace dat z Azure Průzkumník dat pomocí dotazu SQL v Power BI

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Power BI je řešení obchodní analýzy, které umožňuje vizualizovat data a sdílet výsledky v rámci organizace.

Azure Průzkumník dat poskytuje tři možnosti pro připojení k datům v Power BI: použijte integrovaný konektor, importujte dotaz z Azure Průzkumník dat nebo použijte dotaz SQL. V tomto článku se dozvíte, jak pomocí dotazu SQL získat data a vizualizovat je v sestavě Power BI.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete následující:

* E-mailový účet organizace, který je členem Azure Active Directory, abyste se mohli připojit ke [clusteru azure Průzkumník dat Help](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (vyberte **Stáhnout zdarma**)

## <a name="get-data-from-azure-data-explorer"></a>Získat data z Azure Průzkumník dat

Nejprve se připojíte ke clusteru Azure Průzkumník dat Help a pak přenesete podmnožinu dat z tabulky *StormEvents* . [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Obvykle používáte nativní dotazovací jazyk s Azure Průzkumník dat, ale podporuje taky dotazy SQL, které tady použijete. Azure Průzkumník dat překládá dotaz SQL na nativní dotaz za vás.

1. V Power BI Desktop na kartě **Domů** vyberte **získat data** a pak **Další**.

    ![Získání dat](media/power-bi-sql-query/get-data-more.png)

1. Vyhledejte *Azure SQL Database*vyberte **Azure SQL Database** pak **připojit**.

    ![Vyhledání a získání dat](media/power-bi-sql-query/search-get-data.png)

1. Na obrazovce **SQL Server databáze** vyplňte formulář následujícími informacemi.

    ![Databáze, tabulka, možnosti dotazu](media/power-bi-sql-query/database-table-query.png)

    **Nastavení** | **Hodnota** | **Popis pole**
    |---|---|---|
    | Server | *help.kusto.windows.net* | Adresa URL pro cluster s nápovědu (bez *https://* ). Pro jiné clustery je adresa URL ve tvaru *\<název_clusteru\>.\<Region\>. kusto.Windows.NET*. |
    | Databáze | *Ukázky* | Ukázková databáze, která je hostována v clusteru, ke kterému se připojujete. |
    | Režim připojení dat | *Import* | Určuje, zda Power BI importuje data nebo se připojí přímo ke zdroji dat. V této spojnici můžete použít jednu z možností. |
    | Časový limit příkazu | Ponechte prázdné | Doba, po kterou se dotaz spustí, než vygeneruje chybu časového limitu. |
    | Příkaz SQL | Kopírovat dotaz pod touto tabulkou | Příkaz jazyka SQL, který Azure Průzkumník dat, se přeloží do nativního dotazu. |
    | Další možnosti | Ponechat jako výchozí hodnoty | Možnosti se nevztahují na clustery Azure Průzkumník dat. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Pokud ještě nemáte připojení ke clusteru podpory, přihlaste se. Přihlaste se pomocí účet Microsoft a pak vyberte **připojit**.

    ![Přihlášení](media/power-bi-sql-query/sign-in.png)

1. Na obrazovce **help.kusto.Windows.NET: Samples** (načíst) vyberte **načíst**.

    ![Načtení dat](media/power-bi-sql-query/load-data.png)

    Tabulka se otevře v hlavním Power BI okně v zobrazení sestav, kde můžete vytvářet sestavy založené na ukázkových datech.

## <a name="visualize-data-in-a-report"></a>Vizualizace dat v sestavě

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete sestavu, kterou jste vytvořili pro tento článek, odstraňte soubor Power BI Desktop (. pbix).

## <a name="next-steps"></a>Další kroky

[Vizualizace dat pomocí konektoru služby Azure Průzkumník dat pro Power BI](power-bi-connector.md)