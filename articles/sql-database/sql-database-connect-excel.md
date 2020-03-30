---
title: Připojení Excelu k jedné databázi
description: Přečtěte si, jak připojit Microsoft Excel k jedné databázi v databázi Azure SQL. Naimportujte si data do Excelu, kde můžete data dále zkoumat a vytvářet z nich sestavy.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
ms.date: 02/12/2019
ms.openlocfilehash: de5f23bf1e8acd8a5fcd0cf8e1526f88667800c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827136"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Připojení Excelu k jedné databázi v databázi Azure SQL a vytvoření sestavy

Připojte Excel k jedné databázi v Azure SQL Database a importujte data a vytvářejte tabulky a grafy založené na hodnotách v databázi. V tomto kurzu nastavíte připojení mezi Excelem a databázovou tabulkou, uložíte soubor, ve kterém jsou uložená data a informace o připojení pro Excel, a pak z hodnot v databázi vytvoříte kontingenční graf.

Než začnete, budete potřebovat jednu databázi. Pokud ji nemáte, přečtěte si informace [o vytvoření jedné databáze](sql-database-single-database-get-started.md) a vytvoření brány firewall IP na úrovni [serveru,](sql-database-server-level-firewall-rule.md) abyste během několika minut zprovoznili jednu databázi s ukázkovými daty.

V tomto článku budete importovat ukázková data do aplikace Excel z tohoto článku, ale můžete postupovat podle podobných kroků s vlastními daty.

Budete také potřebovat Excel. V tomto článku používáme [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Připojení Excelu k databázi SQL a načtení dat

1. Když budete chtít Excel připojit k databázi SQL, otevřete ho a vytvořte nový sešit nebo otevřete existující excelový sešit.
2. V řádku nabídek v horní části stránky vyberte kartu **Data,** vyberte **Získat data**, vyberte Z Azure a pak vyberte Z Azure **SQL Database**. 

   ![Výběr zdroje dat: Připojení Excelu k databázi SQL](./media/sql-database-connect-excel/excel_data_source.png)

   Otevře se Průvodce datovým připojením.
3. V dialogovém okně **Připojit k databázovému serveru** zadejte **název serveru** SQL Database, ke kterému se chcete připojit, v následující podobě: <*název_serveru*>**. database.windows.net**. Například **msftestserver.database.windows.net**. Volitelně zadejte název databáze. Výběrem **možnosti OK** otevřete okno pověření. 

   ![Dialogové okno Připojit k databázovému serveru](media/sql-database-connect-excel/server-name.png)

4. V dialogovém okně **Databáze serveru SQL Server** vyberte na levé straně **položku Databáze** a zadejte uživatelské **jméno** a **heslo** pro databázový server SQL, ke kterému se chcete připojit. Výběrem **možnosti Připojit** otevřete **navigátor**. 

   ![Zadejte název a přihlašovací údaje serveru.](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > V závislosti na vašem síťovém prostředí nemusíte být schopni se připojit nebo může dojít ke ztrátě připojení (pokud server služby SQL Database nepovoluje přenos dat z IP adresy vašeho klienta). Přejděte na [portál Azure](https://portal.azure.com/), klikněte na SQL servery, klikněte na svůj server, v nastavení klikněte na bránu firewall a přidejte IP adresu svého klienta. Podrobnosti najdete v tématu [Jak nakonfigurovat nastavení brány firewall](sql-database-configure-firewall-settings.md).

5. V **navigátoru**vyberte ze seznamu databázi, se kterou chcete pracovat, vyberte tabulky nebo zobrazení, se kterými chcete pracovat (vybrali jsme **vGetAllCategories**) a pak vyberte **Načíst,** chcete-li přesunout data z databáze do excelové tabulky.

    ![Vyberte databázi a tabulku.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Import dat do Excelu a vytvoření kontingenčního grafu

Nyní, když jste navázali připojení, máte několik různých možností, jak načíst data. Následující kroky například vytvoří kontingenční graf založený na datech nalezených v databázi SQL. 

1. Postupujte podle kroků v předchozí části, ale tentokrát místo výběru **možnosti Načíst**vyberte možnost **Načíst do** rozevíracího rozevíracího **políčka Načíst.**
2. Dále vyberte, jak chcete tato data zobrazit v sešitu. Zvolili jsme možnost **Kontingenční graf**. Můžete také zvolit možnost **Nový list** nebo **Přidat tato data do datového modelu**. Další informace o datových modelech najdete v tématu [Vytvoření datového modelu v Excelu](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![Volba formátu pro data v Excelu](./media/sql-database-connect-excel/import-data.png)

    List má teď prázdnou kontingenční tabulku a graf.
3. V části **Pole kontingenční tabulky** zaškrtněte políčka pro všechna pole, která chcete zobrazit.

    ![Nakonfigurujte sestavu databáze.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Pokud chcete k databázi připojit další excelové sešity a listy, vyberte kartu **Data** a vyberte **Poslední zdroje** a spusťte dialogové okno **Poslední zdroje.** Odtud vyberte připojení, které jste vytvořili ze seznamu, a klepněte na tlačítko **Otevřít**.
> ![Dialogové okno Poslední zdroje](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Vytvoření trvalého připojení pomocí souboru .odc

Chcete-li trvale uložit podrobnosti o připojení, můžete vytvořit soubor .odc a učinit toto připojení volitelnou volbou v dialogovém okně **Existující připojení.** 

1. V řádku nabídek v horní části stránky vyberte kartu **Data** a pak vyberte **Existující připojení** a spusťte dialogové okno **Existující připojení.** 
   1. Vyberte **Vyhledat další** a otevřete dialogové okno **Vybrat zdroj dat.**   
   2. Vyberte soubor **+NewSqlServerConnection.odc** a pak klepnutím na **možnost Otevřít** otevřete **Průvodce datovým připojením**.

      ![Dialogové okno Nové připojení](media/sql-database-connect-excel/new-connection.png)

2. V **Průvodci datovým připojením**zadejte název serveru a pověření databáze SQL. Vyberte **další**. 
   1. V rozevíracím souboru vyberte databázi, která obsahuje vaše data. 
   2. Vyberte tabulku nebo zobrazení, které vás zajímají. Vybrali jsme vGetAllCategories.
   3. Vyberte **další**. 

      ![Průvodce datovým připojením](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Na další obrazovce Průvodce datovým připojením vyberte umístění souboru, **název souboru**a **popisný název.** Můžete také zvolit uložení hesla do souboru, i když to může potenciálně vystavit data nežádoucímu přístupu. Vyberte **Dokončit,** až bude připraven. 

    ![Uložit datové připojení](media/sql-database-connect-excel/save-data-connection.png)

4. Vyberte způsob importu dat. Rozhodli jsme se udělat kontingenční tabulku. Vlastnosti připojení můžete také upravit výběrem **možnosti Vlastnosti**. Až budete připraveni, vyberte **ok.** Pokud jste se nerozhodli uložit heslo se souborem, budete vyzváni k zadání pověření. 

    ![Import dat](media/sql-database-connect-excel/import-data2.png)

5. Ověřte, zda bylo nové připojení uloženo, rozbalením karty **Data** a výběrem **možnosti Existující připojení**. 

    ![Existující připojení](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak se [připojit k SQL Database přes SQL Server Management Studio](sql-database-connect-query-ssms.md) a provádět pokročilé dotazy a analýzy.
* Další informace o výhodách [elastických fondů](sql-database-elastic-pool.md).
* Zjistěte, jak [vytvořit webovou aplikaci, která se připojuje k SQL Database na back-endu](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
