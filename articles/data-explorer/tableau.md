---
title: Použití připojení rozhraní ODBC (Open Database Connectivity) k Azure Průzkumník dat k vizualizaci dat pomocí Tableau
description: V tomto článku se dozvíte, jak používat připojení rozhraní ODBC (Open Database Connectivity) k Azure Průzkumník dat připojení k vizualizaci dat pomocí Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 4dd8fbd761a3442536919e17bae5465adf6b945f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023867"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Vizualizace dat z Azure Průzkumník dat v Tableau

 [Tableau](https://www.tableau.com/) je platforma pro vizuální analýzy Business Intelligence. Pokud se chcete připojit k Azure Průzkumník dat z Tableau a dostat data z ukázkového clusteru, použijte SQL Server ovladač ODBC (Open Database Connectivity). 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete následující:

* [Připojte se k azure Průzkumník dat pomocí rozhraní ODBC](connect-odbc.md) pomocí SQL Server ovladače ODBC, abyste se připojili ke službě Azure Průzkumník dat z Tableau. 

* Tableau Desktop, úplnou nebo [zkušební](https://www.tableau.com/products/desktop/download) verzi.

* Cluster, který obsahuje ukázková data StormEvents. Další informace najdete v tématu [Vytvoření clusteru azure Průzkumník dat a databáze](create-cluster-database-portal.md) a ingestování [ukázkových dat do Azure Průzkumník dat](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Vizualizace dat v Tableau 

Jakmile dokončíte konfiguraci rozhraní ODBC, můžete do Tableau přenést ukázková data.

1. V Tableau desktopu v levé nabídce vyberte **Další databáze (ODBC)** .

    ![Připojení přes ODBC](media/tableau/connect-odbc.png)

1. V poli **DSN**vyberte zdroj dat, který jste vytvořili pro rozhraní ODBC, a pak vyberte **Přihlásit**se.

    ![Přihlášení ODBC](media/tableau/odbc-sign-in.png)

1. V části **databáze**vyberte databázi v ukázkovém clusteru, například *TestDatabase*. V poli **schéma**vyberte položku *dbo*a v části **tabulka**vyberte ukázkovou tabulku *StormEvents* .

    ![Vybrat databázi a tabulku](media/tableau/select-database-table.png)

1. Tableau nyní zobrazuje schéma pro ukázková data. Pokud chcete data přenést do Tableauu, vyberte **aktualizovat hned** .

    ![Aktualizace dat](media/tableau/update-data.png)

    Když se data naimportují, Tableau zobrazí řádky dat podobné následujícímu obrázku.

    ![Sada výsledků](media/tableau/result-set.png)

1. V Tableau teď můžete vytvářet vizualizace na základě dat, která jste pronesli v Azure Průzkumník dat. Další informace najdete v tématu [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Další kroky

* [Psaní dotazů pro Azure Data Explorer](write-queries.md)