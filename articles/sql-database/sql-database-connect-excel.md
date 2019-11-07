---
title: Připojení Excelu k jedné databázi v Azure SQL Database
description: Naučte se připojit Microsoft Excel k izolované databázi ve službě Azure SQL Database. Naimportujte si data do Excelu, kde můžete data dále zkoumat a vytvářet z nich sestavy.
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
ms.openlocfilehash: 603aa52aefc4c4d1200d433f7f73f6070849c8f7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691031"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Připojení Excelu k izolované databázi ve službě Azure SQL Database a vytvoření sestavy

Připojení Excelu k izolované databázi v Azure SQL Database a import dat a vytváření tabulek a grafů na základě hodnot v databázi. V tomto kurzu nastavíte připojení mezi Excelem a databázovou tabulkou, uložíte soubor, ve kterém jsou uložená data a informace o připojení pro Excel, a pak z hodnot v databázi vytvoříte kontingenční graf.

Než začnete pracovat, budete potřebovat jednu databázi. Pokud ho nemáte, přečtěte si téma [vytvoření jedné databáze](sql-database-single-database-get-started.md) a [Vytvoření brány firewall protokolu IP na úrovni serveru](sql-database-server-level-firewall-rule.md) pro získání izolované databáze s ukázkovými daty v několika minutách.

V tomto článku naimportujete do Excelu ukázková data z tohoto článku, ale můžete postupovat podle podobných kroků s vlastními daty.

Budete také potřebovat Excel. V tomto článku používáme [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Připojení Excelu k databázi SQL a načtení dat

1. Když budete chtít Excel připojit k databázi SQL, otevřete ho a vytvořte nový sešit nebo otevřete existující excelový sešit.
2. V řádku nabídek v horní části stránky vyberte kartu **data** , vyberte **získat data**, vyberte z Azure a pak vyberte **z Azure SQL Database**. 

   ![Výběr zdroje dat: Připojení Excelu k databázi SQL](./media/sql-database-connect-excel/excel_data_source.png)

   Otevře se Průvodce datovým připojením.
3. V dialogovém okně **Připojit k databázovému serveru** zadejte **název serveru** SQL Database, ke kterému se chcete připojit, v následující podobě: <*název_serveru*> **. database.windows.net**. Například **msftestserver.Database.Windows.NET**. V případě potřeby zadejte název vaší databáze. Výběrem **OK** otevřete okno přihlašovací údaje. 

   ![Dialogové okno připojit k databázovému serveru](media/sql-database-connect-excel/server-name.png)

4. V dialogovém okně **SQL Server databáze** vyberte v levé části možnost **databáze** a potom zadejte **uživatelské jméno** a **heslo** pro SQL Database Server, ke kterému se chcete připojit. Kliknutím na **připojit** otevřete **navigátor**. 

   ![Zadejte název a přihlašovací údaje serveru.](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > V závislosti na vašem síťovém prostředí nemusíte být schopni se připojit nebo může dojít ke ztrátě připojení (pokud server služby SQL Database nepovoluje přenos dat z IP adresy vašeho klienta). Přejděte na [portál Azure](https://portal.azure.com/), klikněte na SQL servery, klikněte na svůj server, v nastavení klikněte na bránu firewall a přidejte IP adresu svého klienta. Podrobnosti najdete v tématu [Jak nakonfigurovat nastavení brány firewall](sql-database-configure-firewall-settings.md).

5. V **navigátoru**vyberte v seznamu databázi, se kterou chcete pracovat, vyberte tabulky nebo zobrazení, se kterými chcete pracovat (zvolili jsme **vGetAllCategories**), a pak vyberte **načíst** , chcete-li přesunout data z databáze do tabulky aplikace Excel.

    ![Vyberte databázi a tabulku.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Import dat do Excelu a vytvoření kontingenčního grafu

Teď, když jste navázali připojení, máte k dispozici několik různých možností, jak načíst data. Následující kroky například vytvoří kontingenční graf na základě dat nalezených ve vašem SQL Database. 

1. Postupujte podle kroků v předchozí části, ale tentokrát místo výběru možnosti **načíst**vyberte **načíst do** v rozevíracím seznamu **načíst** .
2. V dalším kroku vyberte, jak chcete zobrazit tato data v sešitu. Zvolili jsme možnost **Kontingenční graf**. Můžete také zvolit možnost **Nový list** nebo **Přidat tato data do datového modelu**. Další informace o datových modelech najdete v tématu [Vytvoření datového modelu v Excelu](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![Volba formátu pro data v Excelu](./media/sql-database-connect-excel/import-data.png)

    List má teď prázdnou kontingenční tabulku a graf.
3. V části **Pole kontingenční tabulky** zaškrtněte políčka pro všechna pole, která chcete zobrazit.

    ![Nakonfigurujte sestavu databáze.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Pokud chcete k databázi připojit jiné excelové sešity a listy, vyberte kartu **data** a vyberte **Poslední zdroje** . otevře se dialogové okno **Poslední zdroje** . Odtud ze seznamu vyberte připojení, které jste vytvořili, a pak klikněte na **otevřít**.
> Dialogové okno ![poslední zdroje](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Vytvoření trvalého připojení pomocí souboru ODC

Chcete-li uložit podrobnosti o připojení trvale, můžete vytvořit soubor. odc a vytvořit toto připojení možnost s možností volby v dialogovém okně **existující připojení** . 

1. V řádku nabídek v horní části stránky vyberte kartu **data** a pak vyberte **existující připojení** . tím otevřete dialogové okno **existující připojení** . 
   1. Pokud chcete otevřít dialogové okno **Vybrat zdroj dat** , vyberte **Vyhledat další** .   
   2. Vyberte soubor **+ NewSqlServerConnection. odc** a pak výběrem **otevřít** otevřete **Průvodce datovým připojením**.

      ![Nové připojení – dialogové okno](media/sql-database-connect-excel/new-connection.png)

2. V **Průvodci datovým připojením**zadejte název serveru a přihlašovací údaje pro SQL Database. Vyberte **Next** (Další). 
   1. Z rozevíracího seznamu vyberte databázi, která obsahuje vaše data. 
   2. Vyberte tabulku nebo zobrazení, na které vás zajímáte. Zvolili jsme vGetAllCategories.
   3. Vyberte **Next** (Další). 

      ![Průvodce datovým připojením](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Na další obrazovce Průvodce datovým připojením vyberte umístění souboru, **název souboru**a **popisný název** . Můžete také zvolit uložení hesla do souboru, i když to může potenciálně vystavit data nežádoucímu přístupu. Po **dokončení vyberte Dokončit** . 

    ![Uložit datové připojení](media/sql-database-connect-excel/save-data-connection.png)

4. Vyberte, jak chcete importovat data. Zvolili jsme vytvoření kontingenční tabulky. Vlastnosti připojení můžete změnit také tak, že vyberete **vlastnosti**. Až budete připraveni, vyberte **OK** . Pokud jste se nerozhodli Uložit heslo se souborem, budete vyzváni k zadání přihlašovacích údajů. 

    ![Import dat](media/sql-database-connect-excel/import-data2.png)

5. Rozbalením karty **data** a vybráním **existujících připojení**ověřte, zda bylo nové připojení uloženo. 

    ![Existující připojení](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak se [připojit k SQL Database přes SQL Server Management Studio](sql-database-connect-query-ssms.md) a provádět pokročilé dotazy a analýzy.
* Další informace o výhodách [elastických fondů](sql-database-elastic-pool.md).
* Zjistěte, jak [vytvořit webovou aplikaci, která se připojuje k SQL Database na back-endu](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
