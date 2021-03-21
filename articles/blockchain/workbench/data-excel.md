---
title: Použití dat služby Azure Blockchain Workbench v Microsoft Excelu
description: Naučte se, jak načíst a zobrazit Azure blockchain Workbench Preview data SQL DB v Microsoft Excelu.
ms.date: 09/05/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: 4dd941abdded6a93510f4a71119769cd73855bcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000160"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Zobrazení dat služby Azure Blockchain Workbench v Microsoft Excelu

K zobrazení dat z databáze SQL DB služby Azure Blockchain Workbench můžete použít Microsoft Excel. Tento článek obsahuje postup pro:

* Připojení k databázi služby Blockchain Workbench z Microsoft Excelu
* Prohlédnutí databázových tabulek a zobrazení databáze služby Blockchain Workbench
* Načtení dat zobrazení služby Blockchain Workbench do Excelu

## <a name="connect-to-the-blockchain-workbench-database"></a>Připojení k databázi Blockchain Workbench

Postup připojení k databázi Blockchain Workbench:

1. Otevřete Microsoft Excel.
2. Na kartě **Data** zvolte **Načíst data**.
3. Vyberte **Z Azure** a potom vyberte **Ze služby Azure SQL Database**.

   ![Připojení k Azure SQL Database](./media/data-excel/connect-sql-db.png)

4. V dialogovém okně **Databáze SQL Serveru**:

    * Do pole **Server** zadejte název serveru služby Blockchain Workbench.
    * Do pole **Databáze (nepovinné)** zadejte název databáze.

   ![Zadání databázového serveru a databáze](./media/data-excel/provide-server-db.png)

5. V navigačním panelu dialogového okna **Databáze SQL Serveru** vyberte **Databáze**. Zadejte **Uživatelské jméno** a **Heslo** a pak klikněte na **Připojit**.

    > [!NOTE]
    > Pokud používáte přihlašovací údaje vytvořené při nasazení služby Azure Blockchain Workbench, pak **Uživatelským jménem** je `dbadmin`. a **Heslo** je shodné s heslem vytvořeným při nasazení služby Blockchain Workbench.
    
   ![Zadání přihlašovacích údajů pro přístup do databáze](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Prohlédnutí databázových tabulek a zobrazení databáze

Po připojení k databázi se v Excelu otevře dialogové okno Navigátor. Okno Navigátor můžete použít k prohlédnutí zobrazení a tabulek v databázi. Zobrazení jsou navržená pro generování sestav a jejich názvy mají předponu **vw**.

   ![Náhled zobrazení v excelovém okně Navigátor](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Načtení dat zobrazení do excelového sešitu

Další příklad ukazuje, jak můžete načíst data ze zobrazení do excelového sešitu.

1. Z posuvníku okna **Navigátor** vyberte zobrazení **vwContractAction**. Náhled zobrazení **vwContractAction** ukáže všechny akce související s kontraktem v databázi služby Blockchain Workbench.
2. Vyberte **Načíst**, abyste získali všechna data v zobrazení a umístěte je do excelového sešitu.

   ![Data načtená ze zobrazení](./media/data-excel/view-data.png)

Nyní, když máte data načtená, můžete použít funkce Excelu k vytvoření vlastních sestav pomocí metadat a dat transakcí z databáze služby Azure Blockchain Workbench.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazení databáze ve službě Azure Blockchain Workbench](database-views.md)