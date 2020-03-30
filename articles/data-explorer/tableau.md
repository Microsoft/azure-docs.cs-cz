---
title: Vizualizace dat Tableau pomocí konektoru ODBC průzkumníka dat Azure
description: V tomto článku se dozvíte, jak používat připojení k připojení otevřené databáze (ODBC) k připojení Průzkumníka dat Azure k vizualizaci dat pomocí Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562441"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Vizualizace dat z Průzkumníka dat Azure v Tableau

 [Tableau](https://www.tableau.com/) je platforma vizuální analýzy pro business intelligence. Chcete-li se připojit k Průzkumníku dat Azure z Tableau a přenést data z ukázkového clusteru, použijte ovladač PŘIPOJENÍ OTEVŘENÉ DATABÁZE SQL Server (ODBC). 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku je třeba provést následující:

* [Připojte se k Průzkumníku dat Azure pomocí rozhraní ODBC](connect-odbc.md) pomocí ovladače SQL Server ODBC a připojte se k Průzkumníku dat Azure z Tableau. 

* Tableau Desktop, plná nebo [zkušební](https://www.tableau.com/products/desktop/download) verze.

* Cluster, který obsahuje ukázková data StormEvents. Další informace najdete [v tématu Vytvoření clusteru A databáze Azure Průzkumníka dat](create-cluster-database-portal.md) a [ukázkových dat ingestování do Průzkumníka dat Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Vizualizace dat v Tableau 

Po dokončení konfigurace rozhraní ODBC můžete přenést ukázková data do tableau.

1. V tableau desktop, v levém menu, vyberte **jiné databáze (ODBC)**.

    ![Připojení přes ODBC](media/tableau/connect-odbc.png)

1. V **aplikaci DSN**vyberte zdroj dat, který jste vytvořili pro rozhraní ODBC, a pak **vyberte Přihlásit se**.

    ![Přihlášení od bc](media/tableau/odbc-sign-in.png)

1. V **části Databáze**vyberte databázi v ukázkovém clusteru, například *TestDatabase*. V **části Schéma**vyberte *dbo*a **tabulka**vyberte ukázkovou tabulku *StormEvents.*

    ![Vybrat databázi a tabulku](media/tableau/select-database-table.png)

1. Tableau nyní zobrazuje schéma pro ukázková data. Chcete-li data přenést do tableau, vyberte **možnost Aktualizovat nyní.**

    ![Aktualizace dat](media/tableau/update-data.png)

    Při importu dat Tableau zobrazí řádky dat podobné na následujícím obrázku.

    ![Sada výsledků](media/tableau/result-set.png)

1. Teď můžete vytvářet vizualizace v Tableau na základě dat, která jste přinesli z Azure Data Exploreru. Další informace naleznete v [tématu Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Další kroky

* [Psaní dotazů pro Azure Data Explorer](write-queries.md)