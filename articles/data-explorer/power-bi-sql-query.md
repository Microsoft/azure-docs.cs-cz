---
title: Vizualizace dat z Azure Data Exploreru pomocí dotazu Power BI SQL
description: 'V tomto článku se dozvíte, jak použít jednu ze tří možností pro vizualizaci dat v Power BI: dotaz SQL proti clusteru Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: d402d4c1ee77d0f97d2a5c3bdf43d0cc62aac096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560469"
---
# <a name="visualize-data-from-azure-data-explorer-using-a-sql-query-in-power-bi"></a>Vizualizace dat z Azure Data Exploreru pomocí dotazu SQL v Power BI

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Power BI je řešení obchodní analýzy, které umožňuje vizualizovat data a sdílet výsledky v rámci organizace.

Azure Data Explorer nabízí tři možnosti pro připojení k datům v Power BI: použít integrovaný konektor, importovat dotaz z Azure Data Exploreru nebo použít dotaz SQL. V tomto článku se ukazuje, jak pomocí dotazu SQL získat data a vizualizovat je v sestavě Power BI.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku je třeba provést následující:

* E-mailový účet organizace, který je členem adresáře Azure Active Directory, takže se můžete připojit k [clusteru nápovědy Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (zvolte **STÁHNOUT ZDARMA)**

## <a name="get-data-from-azure-data-explorer"></a>Získání dat z Průzkumníka dat Azure

Nejprve se připojíte ke clusteru nápovědy Azure Data Explorer, pak přenést podmnožinu dat z tabulky *StormEvents.* [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

S Azure Data Explorer se obvykle používá nativní dotazovací jazyk, ale podporuje také dotazy SQL, které tady použijete. Azure Data Explorer převede dotaz SQL na nativní dotaz za vás.

1. V Power BI Desktopu na kartě **Domů** vyberte **Získat data** a pak **Další**.

    ![Získání dat](media/power-bi-sql-query/get-data-more.png)

1. Vyhledejte *Azure SQL Database*, vyberte Azure SQL **Database** a pak **Connect**.

    ![Vyhledání a získání dat](media/power-bi-sql-query/search-get-data.png)

1. Na obrazovce **databáze serveru SQL Server** vyplňte formulář s následujícími informacemi.

    ![Možnosti databáze, tabulky, dotazu](media/power-bi-sql-query/database-table-query.png)

    **Nastavení** | **Hodnotu** | **Popis pole**
    |---|---|---|
    | Server | *help.kusto.windows.net* | Adresa URL clusteru nápovědy (bez *https://).* U jiných clusterů je adresa URL ve formuláři * \<Název clusteru\>.\< Oblast\>.kusto.windows.net*. |
    | Databáze | *ukázky* | Ukázková databáze, která je hostována v clusteru, ke kterému se připojujete. |
    | Režim datového připojení | *Import* | Určuje, jestli Power BI importuje data nebo se připojí přímo ke zdroji dat. S tímto konektorem můžete použít obě možnosti. |
    | Časový čas příkazu | Ponechte prázdné | Jak dlouho dotaz spustí před vyvolá chybu časového limitu. |
    | Příkaz SQL | Zkopírovat dotaz pod touto tabulkou | Příkaz SQL, který Azure Data Explorer překládá do nativního dotazu. |
    | Další možnosti | Ponechat jako výchozí hodnoty | Možnosti se nevztahují na clustery Průzkumníka dat Azure. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Pokud ještě nemáte připojení ke clusteru nápovědy, přihlaste se. Přihlaste se pomocí účtu Microsoft a pak vyberte **Připojit**.

    ![Přihlášení](media/power-bi-sql-query/sign-in.png)

1. Na **obrazovce help.kusto.windows.net: Ukázky** vyberte **Načíst**.

    ![Načtení dat](media/power-bi-sql-query/load-data.png)

    Tabulka se otevře v hlavním okně Power BI v zobrazení sestavy, kde můžete vytvářet sestavy na základě ukázkových dat.

## <a name="visualize-data-in-a-report"></a>Vizualizace dat v sestavě

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete sestavu, kterou jste pro tento článek vytvořili, odstraňte soubor Power BI Desktop (.pbix).

## <a name="next-steps"></a>Další kroky

[Vizualizace dat pomocí konektoru Azure Data Explorer pro Power BI](power-bi-connector.md)