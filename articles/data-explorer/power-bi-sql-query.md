---
title: 'Rychlý start: Vizualizujte data pomocí jazyka SQL v Power BI'
description: 'V tomto rychlém startu se dozvíte, jak chcete použít jeden z těchto tří možností pro vizualizace dat v Power BI: dotaz SQL proti clusteru služby Průzkumník dat Azure.'
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/14/2018
ms.openlocfilehash: fb9b919621174f38520ab5365d4a67abc8095631
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854798"
---
# <a name="quickstart-visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Rychlý start: Vizualizujte data pomocí Průzkumníku dat Azure konektoru pro Power BI

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Power BI je řešení obchodní analýzy, které umožňuje vizualizovat data a sdílet výsledky v rámci organizace.

Průzkumník služby Azure Data poskytuje tři možnosti připojení k datům v Power BI: použití integrovaného konektoru importovat dotaz v Průzkumníku dat Azure a použít dotaz SQL. V tomto rychlém startu se dozvíte, jak získat data a vizualizace v sestavě Power BI pomocí jazyka SQL.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat k dokončení tohoto rychlého startu:

* Účet organizace e-mailu, který je členem skupiny Azure Active directory, abyste se mohli připojit k [clusteru help Průzkumník dat Azure](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (vyberte **DOWNLOAD FREE**)

## <a name="get-data-from-azure-data-explorer"></a>Získání dat z Průzkumníku dat Azure

Nejprve připojte ke clusteru help Průzkumník dat Azure a pak přenést v podmnožinu dat z *StormEvents* tabulky. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Obvykle použijete nativní dotazovací jazyk pomocí Průzkumníku dat Azure, ale také podporuje dotazy SQL, které zde použijete. Průzkumník služby Azure Data překládá příkaz jazyka SQL do nativního dotazu za vás.

1. V Power BI Desktopu na **Domů** kartu, vyberte možnost **získat Data** pak **Další**.

    ![Získání dat](media/power-bi-sql-query/get-data-more.png)

1. Vyhledejte *Azure SQL Database*vyberte **Azure SQL Database** pak **připojit**.

    ![Vyhledání a získání dat](media/power-bi-sql-query/search-get-data.png)

1. Na **databáze systému SQL Server** obrazovky, vyplňte formulář s následujícími informacemi.

    ![Databáze, tabulky, možnosti dotazu](media/power-bi-sql-query/database-table-query.png)

    **Nastavení** | **Hodnota** | **Popis pole**
    |---|---|---|
    | Server | *help.kusto.Windows.NET* | Adresa URL clusteru help (bez *https://*). Pro další clustery, adresa URL je ve formě  *\<ClusterName\>.\< Oblast\>. kusto.windows.net*. |
    | Databáze | *Ukázky* | Ukázkovou databázi, která je hostovaná v clusteru, ke kterému se připojujete. |
    | Režim připojení dat | *Import* | Určuje, jestli Power BI dokončí import dat nebo připojuje přímo ke zdroji dat. |
    | Časový limit příkazu | Ponechte prázdné | Jak dlouho dotaz spustí předtím, než vyvolá vypršení časového limitu. |
    | Příkaz jazyka SQL | Zkopírujte tento dotaz dál v této tabulce | Příkaz SQL, který překládá Průzkumník dat Azure do nativního dotazu. |
    | Další možnosti | Ponechte výchozí hodnoty | Možnosti se nevztahují ke clusterům Průzkumník dat Azure. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Pokud ještě nemáte připojení ke clusteru pomoc, přihlaste se. Přihlaste se pomocí účtu Microsoft a pak vyberte **připojit**.

    ![Přihlášení](media/power-bi-sql-query/sign-in.png)

1. Na **help.kusto.windows.net: ukázky** obrazovky, vyberte **zatížení**.

    ![Načtení dat](media/power-bi-sql-query/load-data.png)

    Tabulka se otevře v hlavního okna Power BI, v zobrazení sestav, kde můžete vytvořit sestavy založené na ukázková data.

## <a name="visualize-data-in-a-report"></a>Vizualizace dat v sestavě

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete sestavy, kterou jste vytvořili pro účely tohoto rychlého startu, odstraňte soubor Power BI Desktopu (.pbix).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlý start: Vizualizujte data pomocí importovaných dotazů v Power BI](power-bi-connector.md)