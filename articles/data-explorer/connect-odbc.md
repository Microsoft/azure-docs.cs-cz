---
title: Připojit do Průzkumníku dat Azure s rozhraním ODBC
description: V tomto návodu se dozvíte, jak chcete nastavit připojení k rozhraní ODBC do Průzkumníku dat Azure a použít toto připojení k vizualizaci dat s Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: d01c825e50e30e3545a0d47e432835c658d677af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60448346"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Připojit do Průzkumníku dat Azure s rozhraním ODBC

Otevřete připojení k databázi ([ODBC](/sql/odbc/reference/odbc-overview)) je široce přijaté aplikačního programovacího rozhraní (API) pro přístup k databázi. Použití rozhraní ODBC pro připojení do Průzkumníku dat Azure z aplikací, které nemají vyhrazené konektoru.

Na pozadí aplikace volat funkce v rozhraní ODBC, které jsou implementovány v modulů specifické pro databázi s názvem *ovladače*. Průzkumník služby Azure Data podporuje podmnožinu komunikační protokol serveru SQL Server ([MS-TDS](/azure/kusto/api/tds/)); proto můžete použít ovladač ODBC pro SQL Server.

V tomto článku se dozvíte, jak použít ovladač ODBC systému SQL Server, abyste se mohli připojit do Průzkumníku dat Azure z libovolné aplikace, která podporuje rozhraní ODBC. Můžete pak volitelně připojit do Průzkumníku dat Azure z Tableau a přenést data z ukázkové clusteru.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat následující dokončete tento postup:

* [Ovladač Microsoft ODBC pro SQL Server verze 17.2.0.1 nebo novější](/sql/connect/odbc/download-odbc-driver-for-sql-server) pro váš operační systém.

* Pokud chcete postupovat podle náš příklad Tableau, budete také potřebovat:

  * Desktop tableau, úplná nebo [zkušební](https://www.tableau.com/products/desktop/download) verze.

  * Clusteru, který obsahuje ukázková data StormEvents. Další informace najdete v tématu [rychlý start: Vytvoření clusteru Průzkumník dat Azure a databáze](create-cluster-database-portal.md) a [Ingestování ukázková data do Průzkumníku dat Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>Konfigurace zdroje dat ODBC

Postupujte podle těchto kroků a nakonfigurujte pomocí ovladače ODBC pro SQL Server zdroje dat ODBC.

1. Ve Windows, vyhledejte *zdroje dat ODBC*a otevřete aplikaci klasické pracovní plochy zdroje dat ODBC.

1. Vyberte **Přidat**.

    ![Přidání zdroje dat](media/connect-odbc/add-data-source.png)

1. Vyberte **ovladač ODBC Driver 17 pro SQL Server** pak **Dokončit**.

    ![Vyberte ovladač](media/connect-odbc/select-driver.png)

1. Zadejte název a popis pro připojení a clusteru, kterou chcete připojit, vyberte **Další**. Adresa URL by měla být ve tvaru clusteru  *\<ClusterName\>.\< Oblast\>. kusto.windows.net*.

    ![Vybrat server](media/connect-odbc/select-server.png)

1. Vyberte **integrace služby Active Directory** pak **Další**.

    ![Active Directory – integrované](media/connect-odbc/active-directory-integrated.png)

1. Potom vyberte databázi s ukázkovými daty **Další**.

    ![Změnit výchozí databázi](media/connect-odbc/change-default-database.png)

1. Na další obrazovce všech možností ponechte jako výchozí vyberte **Dokončit**.

1. Vyberte **zdroj dat otestovat**.

    ![Zdroj testovacích dat.](media/connect-odbc/test-data-source.png)

1. Ověřte, že test úspěšně vyberte **OK**. Pokud test nebylo úspěšné, zkontrolujte hodnoty, které jste zadali v předchozích krocích a ujistěte se, že máte dostatečná oprávnění k připojení ke clusteru.

    ![Test byla úspěšná](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>Vizualizace dat v Tableau (volitelné)

Nyní jste dokončili konfiguraci rozhraní ODBC, je možné přenést ukázková data do Tableau.

1. V Desktopu Tableau, v nabídce vlevo vyberte **ostatní databáze (ODBC)**.

    ![Připojení přes ODBC](media/connect-odbc/connect-odbc.png)

1. Pro **DSN**, vyberte zdroj dat, který jste vytvořili pro rozhraní ODBC a potom vyberte **Sign In**.

    ![Přihlášení k rozhraní ODBC](media/connect-odbc/odbc-sign-in.png)

1. Pro **databáze**, vyberte databázi na clusteru ukázka, jako je například *TestDatabase*. Pro **schématu**vyberte *dbo*a pro **tabulky**, vyberte *StormEvents* vzorovou tabulkou.

    ![Vybrat databázi a tabulku](media/connect-odbc/select-database-table.png)

1. Tableau se teď zobrazuje schéma pro ukázková data. Vyberte **aktualizovat** k načítání dat do Tableau.

    ![Aktualizace dat](media/connect-odbc/update-data.png)

    Když se importují data, Tableau zobrazí řádky dat podobně jako na následujícím obrázku.

    ![Sada výsledků dotazu](media/connect-odbc/result-set.png)

1. Nyní můžete vytvořit vizualizace v Tableau podle data, která zahrnutých v Průzkumníku dat Azure. Další informace najdete v tématu [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Další postup

[Psaní dotazů pro Azure Data Explorer](write-queries.md)

[Kurz: Vizualizovat data z Průzkumníka služby Azure Data v Power BI](visualize-power-bi.md)