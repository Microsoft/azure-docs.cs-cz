---
title: Vizualizace dat s Tableau pomocí připojení připojení ODBC (Open Database) do Průzkumníku dat Azure
description: V tomto článku se dozvíte, jak používat připojení k připojení ODBC (Open Database) do Průzkumníku dat Azure připojení k vizualizaci dat s Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: a0948ae35a5c23768df117979db819861ac64529
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499081"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Vizualizace dat v Průzkumníku dat Azure v Tableau

 [Tableau](https://www.tableau.com/) je platforma pro business intelligence visual analytics. K připojení do Průzkumníku dat Azure z Tableau a přenést data z ukázkové clusteru, použijte ovladač SQL serveru připojení ODBC (Open Database). 

## <a name="prerequisites"></a>Požadavky

Budete potřebovat k dokončení tohoto článku:

* [Připojit do Průzkumníku dat Azure s rozhraním ODBC](connect-odbc.md) použití ovladač ODBC systému SQL Server pro připojení do Průzkumníku dat Azure z Tableau. 

* Desktop tableau, plná, nebo [zkušební](https://www.tableau.com/products/desktop/download) verze.

* Clusteru, který obsahuje ukázková data StormEvents. Další informace najdete v tématu [vytvoření clusteru Průzkumník dat Azure a databáze](create-cluster-database-portal.md) a [Ingestování ukázková data do Průzkumníku dat Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Vizualizace dat v Tableau 

Jakmile dokončíte konfiguraci rozhraní ODBC, je možné přenést ukázková data do Tableau.

1. V Desktopu Tableau, v nabídce vlevo vyberte **ostatní databáze (ODBC)** .

    ![Připojení přes ODBC](media/tableau/connect-odbc.png)

1. Pro **DSN**, vyberte zdroj dat, který jste vytvořili pro rozhraní ODBC a potom vyberte **Sign In**.

    ![Přihlášení k rozhraní ODBC](media/tableau/odbc-sign-in.png)

1. Pro **databáze**, vyberte databázi na clusteru ukázka, jako je například *TestDatabase*. Pro **schématu**vyberte *dbo*a pro **tabulky**, vyberte *StormEvents* vzorovou tabulkou.

    ![Vybrat databázi a tabulku](media/tableau/select-database-table.png)

1. Tableau se teď zobrazuje schéma pro ukázková data. Vyberte **aktualizovat** k načítání dat do Tableau.

    ![Aktualizace dat](media/tableau/update-data.png)

    Když se importují data, Tableau zobrazí řádky dat podobně jako na následujícím obrázku.

    ![Sada výsledků dotazu](media/tableau/result-set.png)

1. Nyní můžete vytvořit vizualizace v Tableau podle data, která zahrnutých v Průzkumníku dat Azure. Další informace najdete v tématu [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Další postup

* [Psaní dotazů pro Azure Data Explorer](write-queries.md)