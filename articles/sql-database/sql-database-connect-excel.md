---
title: "Připojení Excelu k SQL Database | Dokumentace Microsoftu"
description: "Zjistěte, jak připojit Microsoft Excel k databázi SQL Azure v cloudu. Naimportujte si data do Excelu, kde můžete data dále zkoumat a vytvářet z nich sestavy."
services: sql-database
keywords: "připojení excelu k sql, import dat do excelu"
author: joseidz
manager: jhubbard
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 03/10/2017
ms.author: craigg
ms.openlocfilehash: 64896e87bf049c3ccd58f060ac27f35787d4ce4a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Připojení Excelu k databázi Azure SQL a vytvoření sestavy

Připojení Excelu k databázi SQL v cloudu a importovat data a vytvářet tabulky a grafy na základě hodnot v databázi. V tomto kurzu nastavíte připojení mezi Excelem a databázovou tabulkou, uložíte soubor, ve kterém jsou uložená data a informace o připojení pro Excel, a pak z hodnot v databázi vytvoříte kontingenční graf.

Než začnete, budete potřebovat databázi SQL v Azure. Pokud žádnou nemáte, podívejte se na téma [Vytvoření první databáze SQL](sql-database-get-started-portal.md), kde najdete informace o tom, jak si během několika málo minut zprovoznit databázi s ukázkovými daty. V tomto článku budete importovat ukázková data do aplikace Excel z tohoto článku, ale můžete provést kroky pro podobné s vlastními daty.

Budete také potřebovat Excel. V tomto článku používáme [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>Připojení Excelu k databázi SQL a vytvoření souboru odc
1. Když budete chtít Excel připojit k databázi SQL, otevřete ho a vytvořte nový sešit nebo otevřete existující excelový sešit.
2. V řádku nabídek v horní části stránky klikněte na **Data**, **Z jiných zdrojů** a potom klikněte na **Z SQL Serveru**.
   
   ![Výběr zdroje dat: Připojení Excelu k databázi SQL](./media/sql-database-connect-excel/excel_data_source.png)
   
   Otevře se Průvodce datovým připojením.
3. V dialogovém okně **Připojit k databázovému serveru** zadejte **název serveru** SQL Database, ke kterému se chcete připojit, v následující podobě: <*název_serveru*>**. database.windows.net**. Například **adworkserver.database.windows.net**.
4. V části **Přihlašovací pověření** klikněte na **Použít následující uživatelské jméno a heslo**, zadejte **uživatelské jméno** a **heslo**, které jste nastavili pro server SQL Database, když jste ho vytvářeli, a potom klikněte na **Další**.
   
   ![Zadejte název a přihlašovací údaje serveru.](./media/sql-database-connect-excel/connect-to-server.png)
   
   > [!TIP]
   > V závislosti na vašem síťovém prostředí nemusíte být schopni se připojit nebo může dojít ke ztrátě připojení (pokud server databáze SQL nepovoluje přenos dat z IP adresy vašeho klienta). Přejděte na [portál Azure](https://portal.azure.com/), klikněte na SQL servery, klikněte na svůj server, v nastavení klikněte na bránu firewall a přidejte IP adresu svého klienta. Podrobnosti najdete v tématu [Jak nakonfigurovat nastavení brány firewall](sql-database-configure-firewall-settings.md).
   > 
   > 
5. V dialogovém okně **Vybrat databázi a tabulku** vyberte ze seznamu databázi, se kterou chcete pracovat, a potom klikněte na tabulky nebo zobrazení, se kterými chcete pracovat (my jsme zvolili **vGetAllCategories**). Potom klikněte na **Další**.
   
    ![Vyberte databázi a tabulku.](./media/sql-database-connect-excel/select-database-and-table.png)
   
    Otevře se dialogové okno **Uložit soubor datového připojení a dokončit**, kde zadáte informace o souboru databázového připojení (*.odc) Office, který používá Excel. Můžete nechat nastavené výchozí hodnoty nebo si vybrané možnosti přizpůsobit.
6. Můžete nechat nastavené výchozí hodnoty, ale důležité je poznamenat si **název souboru**. **Popis**, **popisný název** a **klíčová slova pro vyhledávání** vám a dalším uživatelům pomůžou zapamatovat si, k jaké databázi se připojujete, a najít připojení. Pokud chcete, aby se informace o připojení uložily do souboru odc, aby se mohl aktualizovat, když se k němu připojíte, klikněte na **Vždy aktualizovat data pomocí tohoto souboru** a potom klikněte na **Dokončit**.
   
    ![Uložení souboru odc](./media/sql-database-connect-excel/save-odc-file.png)
   
    Zobrazí se dialogové okno **Importovat data**.

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Import dat do Excelu a vytvoření kontingenčního grafu
Máte vytvořené připojení a také soubor s daty a informacemi o připojení, můžete tedy začít s importem dat.

1. V dialogovém okně **Importovat data** klikněte na možnost, kterou chcete použít pro prezentování vašich dat v listu, a potom klikněte na **OK**. Zvolili jsme možnost **Kontingenční graf**. Můžete také zvolit možnost **Nový list** nebo **Přidat tato data do datového modelu**. Další informace o datových modelech najdete v tématu [Vytvoření datového modelu v Excelu](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). Klikněte na **Vlastnosti** a prozkoumejte informace o souboru odc, který jste vytvořili v předchozím kroku. Pak zvolte možnosti pro aktualizaci dat.
   
    ![Volba formátu pro data v Excelu](./media/sql-database-connect-excel/import-data.png)
   
    List má teď prázdnou kontingenční tabulku a graf.
2. V části **Pole kontingenční tabulky** zaškrtněte políčka pro všechna pole, která chcete zobrazit.
   
    ![Nakonfigurujte sestavu databáze.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Pokud chcete k databázi připojit jiné excelové sešity a listy, klikněte na **Data**, klikněte na **Připojení**, klikněte na **Přidat**, ze seznamu zvolte připojení, které jste vytvořili, a pak klikněte na **Otevřít**.
> ![Otevření připojení z jiného sešitu](./media/sql-database-connect-excel/open-from-another-workbook.png)
> 
> 

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak se [připojit k SQL Database přes SQL Server Management Studio](sql-database-connect-query-ssms.md) a provádět pokročilé dotazy a analýzy.
* Další informace o výhodách [elastických fondů](sql-database-elastic-pool.md).
* Zjistěte, jak [vytvořit webovou aplikaci, která se připojuje k SQL Database na back-endu](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).

