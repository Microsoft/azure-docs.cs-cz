---
title: Propojení s Excelem
description: Naučte se připojit Microsoft Excel k databázi ve službě Azure SQL Database nebo Azure SQL Managed instance. Naimportujte si data do Excelu, kde můžete data dále zkoumat a vytvářet z nich sestavy.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 304ad51bc40c8165c2dc11bb70287491e3d6dfb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444025"
---
# <a name="connect-excel-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance-and-create-a-report"></a>Připojení Excelu k databázi ve službě Azure SQL Database nebo Azure SQL Managed instance a vytvoření sestavy
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Můžete připojit aplikaci Excel k databázi a následně importovat data a vytvářet tabulky a grafy založené na hodnotách v databázi. V tomto kurzu nastavíte připojení mezi Excelem a databázovou tabulkou, uložíte soubor, ve kterém jsou uložená data a informace o připojení pro Excel, a pak z hodnot v databázi vytvoříte kontingenční graf.

Abyste mohli začít, musíte vytvořit databázi. Pokud ho ještě nemáte, přečtěte si téma [Vytvoření databáze v Azure SQL Database](single-database-create-quickstart.md) a [Vytvoření brány firewall protokolu IP na úrovni serveru](firewall-create-server-level-portal-quickstart.md) pro získání databáze s ukázkovými daty po dobu běhu.

V tomto článku naimportujete do Excelu ukázková data z tohoto článku, ale můžete postupovat podle podobných kroků s vlastními daty.

Budete také potřebovat Excel. V tomto článku používáme [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-and-load-data"></a>Připojit Excel a načíst data

1. Chcete-li připojit aplikaci Excel k databázi v SQL Database, otevřete aplikaci Excel a pak vytvořte nový sešit nebo otevřete existující sešit aplikace Excel.
2. V řádku nabídek v horní části stránky vyberte kartu **data** , vyberte **získat data**, vyberte z Azure a pak vyberte **z Azure SQL Database**.

   ![Vyberte zdroj dat: připojení Excelu k SQL Database.](./media/connect-excel/excel_data_source.png)

3. Do dialogového okna **databáze SQL Server** zadejte **název serveru** , ke kterému se chcete připojit, ve tvaru <*servername* > **. Database.Windows.NET**. Například **msftestserver.Database.Windows.NET**. V případě potřeby zadejte název vaší databáze. Výběrem **OK** otevřete okno přihlašovací údaje.

   ![Dialogové okno připojit k databázovému serveru](./media/connect-excel/server-name.png)

4. V dialogovém okně **SQL Server databáze** vyberte v levé části možnost **databáze** a potom zadejte **uživatelské jméno** a **heslo** pro server, ke kterému se chcete připojit. Kliknutím na **připojit** otevřete **navigátor**.

   ![Zadejte název a přihlašovací údaje serveru.](./media/connect-excel/connect-to-server.png)

   > [!TIP]
   > V závislosti na vašem síťovém prostředí se možná nebudete moct připojit nebo může přijít o připojení, pokud Server nepovoluje provoz z IP adresy klienta. Přejděte na [portál Azure](https://portal.azure.com/), klikněte na SQL servery, klikněte na svůj server, v nastavení klikněte na bránu firewall a přidejte IP adresu svého klienta. Podrobnosti najdete v tématu [Jak nakonfigurovat nastavení brány firewall](firewall-configure.md).

5. V **navigátoru**vyberte v seznamu databázi, se kterou chcete pracovat, vyberte tabulky nebo zobrazení, se kterými chcete pracovat (zvolili jsme **vGetAllCategories**), a pak vyberte **načíst** , chcete-li přesunout data z databáze do tabulky aplikace Excel.

    ![Vyberte databázi a tabulku.](./media/connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Import dat do Excelu a vytvoření kontingenčního grafu

Teď, když jste navázali připojení, máte k dispozici několik různých možností, jak načíst data. Následující kroky například vytvoří kontingenční graf na základě dat nalezených ve vaší databázi v SQL Database.

1. Postupujte podle kroků v předchozí části, ale tentokrát místo výběru možnosti **načíst**vyberte **načíst do** v rozevíracím seznamu **načíst** .
2. V dalším kroku vyberte, jak chcete zobrazit tato data v sešitu. Zvolili jsme možnost **Kontingenční graf**. Můžete také zvolit možnost **Nový list** nebo **Přidat tato data do datového modelu**. Další informace o datových modelech najdete v tématu [Vytvoření datového modelu v Excelu](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B).

    ![Volba formátu pro data v Excelu](./media/connect-excel/import-data.png)

    List má teď prázdnou kontingenční tabulku a graf.
3. V části **Pole kontingenční tabulky** zaškrtněte políčka pro všechna pole, která chcete zobrazit.

    ![Nakonfigurujte sestavu databáze.](./media/connect-excel/power-pivot-results.png)

> [!TIP]
> Pokud chcete k databázi připojit jiné excelové sešity a listy, vyberte kartu **data** a vyberte **Poslední zdroje** . otevře se dialogové okno **Poslední zdroje** . Odtud ze seznamu vyberte připojení, které jste vytvořili, a pak klikněte na **otevřít**.
> ![Dialogové okno poslední zdroje](./media/connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Vytvoření trvalého připojení pomocí souboru ODC

Chcete-li uložit podrobnosti o připojení trvale, můžete vytvořit soubor. odc a vytvořit toto připojení možnost s možností volby v dialogovém okně **existující připojení** .

1. V řádku nabídek v horní části stránky vyberte kartu **data** a pak vyberte **existující připojení** . tím otevřete dialogové okno **existující připojení** .
   1. Pokud chcete otevřít dialogové okno **Vybrat zdroj dat** , vyberte **Vyhledat další** .
   2. Vyberte soubor **+ NewSqlServerConnection. odc** a pak výběrem **otevřít** otevřete **Průvodce datovým připojením**.

      ![Nové připojení – dialogové okno](./media/connect-excel/new-connection.png)

2. V **Průvodci datovým připojením**zadejte název serveru a přihlašovací údaje pro SQL Database. Vyberte **Další**.
   1. Z rozevíracího seznamu vyberte databázi, která obsahuje vaše data.
   2. Vyberte tabulku nebo zobrazení, na které vás zajímáte. Zvolili jsme vGetAllCategories.
   3. Vyberte **Další**.

      ![Průvodce datovým připojením](./media/connect-excel/data-connection-wizard.png)

3. Na další obrazovce Průvodce datovým připojením vyberte umístění souboru, **název souboru**a **popisný název** . Můžete také zvolit uložení hesla do souboru, i když to může potenciálně vystavit data nežádoucímu přístupu. Po **dokončení vyberte Dokončit** .

    ![Uložit datové připojení](./media/connect-excel/save-data-connection.png)

4. Vyberte, jak chcete importovat data. Zvolili jsme vytvoření kontingenční tabulky. Vlastnosti připojení můžete změnit také tak, že vyberete **vlastnosti**. Až budete připraveni, vyberte **OK** . Pokud jste se nerozhodli Uložit heslo se souborem, budete vyzváni k zadání přihlašovacích údajů.

    ![Import dat](./media/connect-excel/import-data2.png)

5. Rozbalením karty **data** a vybráním **existujících připojení**ověřte, zda bylo nové připojení uloženo.

    ![Existující připojení](./media/connect-excel/existing-connection.png)

## <a name="next-steps"></a>Další kroky

* Naučte [se připojit a dotazovat se SQL Server Management Studio](connect-query-ssms.md) pro pokročilou dotazování a analýzu.
* Další informace o výhodách [elastických fondů](elastic-pool-overview.md).
* Naučte se [, jak vytvořit webovou aplikaci, která se připojuje k Azure SQL Database na back-endu](../../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
