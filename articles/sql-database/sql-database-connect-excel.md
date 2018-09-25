---
title: Připojení Excelu k SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak připojit Microsoft Excel k databázi SQL Azure v cloudu. Naimportujte si data do Excelu, kde můžete data dále zkoumat a vytvářet z nich sestavy.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
manager: craigg
ms.date: 03/10/2017
ms.openlocfilehash: 8750552499a5112b1a46b2cb4929c029d5e7e3a0
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063825"
---
# <a name="connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Připojení Excelu k databázi Azure SQL a vytvoření sestavy

Připojení Excelu k SQL database v cloudu a importovat data a vytvářet tabulky a grafy na základě hodnot v databázi. V tomto kurzu nastavíte připojení mezi Excelem a databázovou tabulkou, uložíte soubor, ve kterém jsou uložená data a informace o připojení pro Excel, a pak z hodnot v databázi vytvoříte kontingenční graf.

Než začnete, budete potřebovat databázi SQL v Azure. Pokud žádnou nemáte, podívejte se na téma [Vytvoření první databáze SQL](sql-database-get-started-portal.md), kde najdete informace o tom, jak si během několika málo minut zprovoznit databázi s ukázkovými daty. V tomto článku budete importovat vzorová data do aplikace Excel z tohoto článku, ale můžete podle podobných kroků s vlastními daty.

Budete také potřebovat Excel. V tomto článku používáme [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Připojení Excelu k SQL database a k načtení dat
1. Když budete chtít Excel připojit k databázi SQL, otevřete ho a vytvořte nový sešit nebo otevřete existující excelový sešit.
2. V panelu nabídek v horní části stránky vyberte **Data** kartu, vyberte možnost **získat Data**, vyberte z Azure a pak vyberte **z Azure SQL Database**. 
   
   ![Výběr zdroje dat: Připojení Excelu k databázi SQL](./media/sql-database-connect-excel/excel_data_source.png)
   
   Otevře se Průvodce datovým připojením.
3. V dialogovém okně **Připojit k databázovému serveru** zadejte **název serveru** SQL Database, ke kterému se chcete připojit, v následující podobě: <*název_serveru*>**. database.windows.net**. Například **msftestserver.database.windows.net**. Volitelně můžete zadejte název databáze. Vyberte **OK** otevřete okno přihlašovací údaje. 

   ![Server name.png](media/sql-database-connect-excel/server-name.png)

1. V **databázi systému SQL Server** dialogu **databáze** na levé straně na straně a pak zadejte vaše **uživatelské jméno** a **heslo** pro Server služby SQL database, kterou chcete připojit. Vyberte **připojit** otevřít **Navigátor**. 

  ![Zadejte název a přihlašovací údaje serveru.](./media/sql-database-connect-excel/connect-to-server.png)
   
  > [!TIP]
  > V závislosti na vašem síťovém prostředí nemusíte být schopni se připojit nebo může dojít ke ztrátě připojení (pokud server databáze SQL nepovoluje přenos dat z IP adresy vašeho klienta). Přejděte na [portál Azure](https://portal.azure.com/), klikněte na SQL servery, klikněte na svůj server, v nastavení klikněte na bránu firewall a přidejte IP adresu svého klienta. Podrobnosti najdete v tématu [Jak nakonfigurovat nastavení brány firewall](sql-database-configure-firewall-settings.md).
   
   
5. V **Navigátor**vyberte databáze, kterou chcete pracovat v seznamu vyberte tabulky a zobrazení, kterým chcete pracovat s (jsme zvolili **vGetAllCategories**) a pak vyberte **zatížení**pro přesun dat z databáze SQL Azure pro vaše tabulka aplikace excel.
   
    ![Vyberte databázi a tabulku.](./media/sql-database-connect-excel/select-database-and-table.png)
   

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Import dat do Excelu a vytvoření kontingenčního grafu
Teď, když jste vytvořili připojení, máte několik různých možností, jak načíst data. Například následující kroky vytvoření kontingenčního grafu na základě dat v databázi SQL. 

1. Postupujte podle kroků v předchozí části, ale tentokrát místo výběru **zatížení**vyberte **načíst do** z **zatížení** rozevíracího seznamu.
2. Potom vyberte, jak chcete zobrazit tato data v sešitu. Zvolili jsme možnost **Kontingenční graf**. Můžete také zvolit možnost **Nový list** nebo **Přidat tato data do datového modelu**. Další informace o datových modelech najdete v tématu [Vytvoření datového modelu v Excelu](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 
   
    ![Volba formátu pro data v Excelu](./media/sql-database-connect-excel/import-data.png)
   
    List má teď prázdnou kontingenční tabulku a graf.
2. V části **Pole kontingenční tabulky** zaškrtněte políčka pro všechna pole, která chcete zobrazit.
   
    ![Nakonfigurujte sestavu databáze.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Pokud chcete k databázi připojit jiné Excelové sešity a listy, vyberte **Data** kartu a vyberte **poslední zdroje** ke spuštění **poslední zdroje** dialogové okno. Odtud, zvolte připojení, které jste vytvořili ze seznamu a potom klikněte na tlačítko **otevřít**.
> ![Poslední připojení](media/sql-database-connect-excel/recent-connections.png)
 
## <a name="create-a-permanent-connection-using-odc-file"></a>Vytvoření trvalého připojení pomocí souboru ODC
Trvale uložit podrobné informace o připojení, můžete vytvořit soubor ODC a vytvoření volitelnou možnost v rámci tohoto připojení **existující připojení** dialogové okno. 

1. V panelu nabídek v horní části stránky vyberte **Data** kartu a potom vyberte **existující připojení** ke spuštění **existující připojení** dialogové okno. 
    1. Vyberte **vyhledat další** otevřít **vybrat zdroj dat** dialogové okno.   
    2. Vyberte **+NewSqlServerConnection.odc** souboru a pak vyberte **otevřete** otevřít **Průvodce datovým připojením**.

    ![Nové připojení](media/sql-database-connect-excel/new-connection.png)

2. V **Průvodce datovým připojením**, zadejte název vašeho serveru a přihlašovací údaje vaší databáze SQL. Vyberte **Další**. 
    1. Vyberte databázi, která obsahuje vaše data z rozevíracího seznamu. 
    2. Vyberte tabulku nebo zobrazení, které vás zajímají. Rozhodli jsme se vGetAllCategories.
    3. Vyberte **Další**. 

    ![Průvodce datovým připojením](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Vyberte umístění vašeho souboru **název_souboru**a **popisný název** na další obrazovce Průvodce datovým připojením. Můžete také uložit heslo v souboru, i když to může potenciálně vystavit nežádoucí přístup k datům. Vyberte **Dokončit** Jakmile budete připraveni. 

    ![Uložení datové připojení](media/sql-database-connect-excel/save-data-connection.png)

4. Vyberte, jak chcete importovat data. Rozhodli jsme se kontingenční tabulku. Můžete také upravit vlastnosti připojení vyberete **vlastnosti**. Vyberte **OK** Jakmile budete připraveni. Pokud jste nezvolili uložit heslo k souboru, pak budete vyzváni k zadání přihlašovacích údajů. 

    ![Import dat](media/sql-database-connect-excel/import-data2.png)

5. Ověřte, že nové připojení se uložil tak, že rozbalíte **Data** kartu a vyberete **existující připojení**. 

    ![Existující připojení](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Další postup
* Zjistěte, jak se [připojit k SQL Database přes SQL Server Management Studio](sql-database-connect-query-ssms.md) a provádět pokročilé dotazy a analýzy.
* Další informace o výhodách [elastických fondů](sql-database-elastic-pool.md).
* Zjistěte, jak [vytvořit webovou aplikaci, která se připojuje k SQL Database na back-endu](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).

