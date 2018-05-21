---
title: Připojení Excelu k SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak připojit Microsoft Excel k databázi SQL Azure v cloudu. Naimportujte si data do Excelu, kde můžete data dále zkoumat a vytvářet z nich sestavy.
services: sql-database
keywords: připojení excelu k sql, import dat do excelu
author: joseidz
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 03/10/2017
ms.author: craigg
ms.openlocfilehash: 6f2894d65240580346b99d203f8289652d8e6618
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Připojení Excelu k databázi Azure SQL a vytvoření sestavy

Připojení Excelu k databázi SQL v cloudu a importovat data a vytvářet tabulky a grafy na základě hodnot v databázi. V tomto kurzu nastavíte připojení mezi Excelem a databázovou tabulkou, uložíte soubor, ve kterém jsou uložená data a informace o připojení pro Excel, a pak z hodnot v databázi vytvoříte kontingenční graf.

Než začnete, budete potřebovat databázi SQL v Azure. Pokud žádnou nemáte, podívejte se na téma [Vytvoření první databáze SQL](sql-database-get-started-portal.md), kde najdete informace o tom, jak si během několika málo minut zprovoznit databázi s ukázkovými daty. V tomto článku budete importovat ukázková data do aplikace Excel z tohoto článku, ale můžete provést kroky pro podobné s vlastními daty.

Budete také potřebovat Excel. V tomto článku používáme [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Připojení Excelu k SQL database a načtení dat
1. Když budete chtít Excel připojit k databázi SQL, otevřete ho a vytvořte nový sešit nebo otevřete existující excelový sešit.
2. V řádku nabídek v horní části stránky, vyberte **Data** vyberte **načíst Data**, vyberte z Azure a pak vyberte **z Azure SQL Database**. 
   
   ![Výběr zdroje dat: Připojení Excelu k databázi SQL](./media/sql-database-connect-excel/excel_data_source.png)
   
   Otevře se Průvodce datovým připojením.
3. V dialogovém okně **Připojit k databázovému serveru** zadejte **název serveru** SQL Database, ke kterému se chcete připojit, v následující podobě: <*název_serveru*>**. database.windows.net**. Například **msftestserver.database.windows.net**. Volitelně můžete zadejte název databáze. Vyberte **OK** a otevřete okno přihlašovací údaje. 

   ![Server name.png](media/sql-database-connect-excel/server-name.png)

1. V **databáze systému SQL Server** dialogové okno, vyberte **databáze** na levé straně na straně a pak zadejte vaše **uživatelské jméno** a **heslo** pro Databázový server SQL, které chcete připojit. Vyberte **připojit** otevřete **Navigátor**. 

  ![Zadejte název a přihlašovací údaje serveru.](./media/sql-database-connect-excel/connect-to-server.png)
   
  > [!TIP]
  > V závislosti na vašem síťovém prostředí nemusíte být schopni se připojit nebo může dojít ke ztrátě připojení (pokud server databáze SQL nepovoluje přenos dat z IP adresy vašeho klienta). Přejděte na [portál Azure](https://portal.azure.com/), klikněte na SQL servery, klikněte na svůj server, v nastavení klikněte na bránu firewall a přidejte IP adresu svého klienta. Podrobnosti najdete v tématu [Jak nakonfigurovat nastavení brány firewall](sql-database-configure-firewall-settings.md).
   
   
5. V **Navigátor**, vyberte databáze, kterou chcete pracovat ze seznamu vyberte tabulky nebo zobrazení, kterým chcete pracovat s (jsme zvolili **vGetAllCategories**) a potom vyberte **zatížení**pro přesun dat z databáze SQL Azure pro vaše tabulku v aplikaci excel.
   
    ![Vyberte databázi a tabulku.](./media/sql-database-connect-excel/select-database-and-table.png)
   

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Import dat do Excelu a vytvoření kontingenčního grafu
Teď, když jste vytvořili připojení, máte několik možností s jak načíst data. Například následující kroky slouží k vytvoření kontingenčního grafu na základě dat nalezen v databázi SQL. 

1. Postupujte podle kroků v předchozí části, ale tentokrát místo výběru **zatížení**, vyberte **načíst do** z **zatížení** rozevíracího seznamu.
2. Potom vyberte, jak chcete zobrazit tato data v sešitu. Zvolili jsme možnost **Kontingenční graf**. Můžete také zvolit možnost **Nový list** nebo **Přidat tato data do datového modelu**. Další informace o datových modelech najdete v tématu [Vytvoření datového modelu v Excelu](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 
   
    ![Volba formátu pro data v Excelu](./media/sql-database-connect-excel/import-data.png)
   
    List má teď prázdnou kontingenční tabulku a graf.
2. V části **Pole kontingenční tabulky** zaškrtněte políčka pro všechna pole, která chcete zobrazit.
   
    ![Nakonfigurujte sestavu databáze.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Pokud chcete k databázi připojit jiné Excelové sešity a listy, vyberte **Data** a vyberte **poslední zdroje** ke spuštění **poslední zdroje** dialogové okno. Z ní, vyberte připojení, které jste vytvořili v seznamu a pak klikněte na tlačítko **otevřete**.
> ![Poslední připojení](media/sql-database-connect-excel/recent-connections.png)
 
## <a name="create-a-permanent-connection-using-odc-file"></a>Vytvoření trvalého připojení pomocí souboru ODC
Uložit podrobné informace o připojení trvale, můžete vytvořit soubor ODC a ujistěte se, toto připojení lze vybrat možnost v rámci **existující připojení** dialogové okno. 

1. V řádku nabídek v horní části stránky, vyberte **Data** a pak vyberte **existující připojení** ke spuštění **existující připojení** dialogové okno. 
    1. Vyberte **vyhledat další** otevřete **vybrat zdroj dat** dialogové okno.   
    2. Vyberte **+NewSqlServerConnection.odc** souboru a pak vyberte **otevřete** otevřete **Průvodce datovým připojením**.

    ![Nové připojení](media/sql-database-connect-excel/new-connection.png)

2. V **Průvodce datovým připojením**, zadejte název serveru a vaše přihlašovací údaje databáze SQL. Vyberte **Next** (Další). 
    1. Vyberte databázi, která obsahuje vaše data z rozevíracího seznamu. 
    2. Vyberte tabulku nebo zobrazení, které vás zajímají. Jsme zvolili vGetAllCategories.
    3. Vyberte **Next** (Další). 

    ![Průvodce datovým připojením](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Vyberte umístění vašeho souboru **název souboru**a **popisný název** na další obrazovce Průvodce datovým připojením. Také můžete uložit heslo v souboru, i když to může potenciálně vystavit data nežádoucí přístup. Vyberte **Dokončit** Jakmile budete připraveni. 

    ![Uložit datové připojení](media/sql-database-connect-excel/save-data-connection.png)

4. Vyberte, jakým způsobem chcete importovat data. Jsme zvolili udělat kontingenční tabulku. Můžete také upravit vlastnosti připojení po vyberte **vlastnosti**. Vyberte **OK** Jakmile budete připraveni. Pokud jste nezvolili s soubor uložit heslo, pak zobrazí se výzva k zadání přihlašovacích údajů. 

    ![Umožňuje importovat Data](media/sql-database-connect-excel/import-data2.png)

5. Ověřte, že nové připojení se uložila rozšířením **Data** kartě a výběrem **existující připojení**. 

    ![Existující připojení](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Další postup
* Zjistěte, jak se [připojit k SQL Database přes SQL Server Management Studio](sql-database-connect-query-ssms.md) a provádět pokročilé dotazy a analýzy.
* Další informace o výhodách [elastických fondů](sql-database-elastic-pool.md).
* Zjistěte, jak [vytvořit webovou aplikaci, která se připojuje k SQL Database na back-endu](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).

