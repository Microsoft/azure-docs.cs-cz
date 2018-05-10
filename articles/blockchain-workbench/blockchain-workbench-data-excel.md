---
title: Pomocí Azure Blockchain Workbench dat v aplikaci Microsoft Excel
description: Zjistěte, jak načíst a zobrazit Azure Blockchain Workbench SQL DB data v aplikaci Microsoft Excel.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 70297bd0af6322d0f3ac2c719d1827e4bc5898cd
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Zobrazení dat Azure Blockchain Workbench pomocí aplikace Microsoft Excel

Aplikace Microsoft Excel můžete použít k zobrazení dat v databázi SQL Azure Blockchain Workbench. Tento článek obsahuje kroky, které budete muset:

* Připojení k databázi Blockchain Workbench z aplikace Microsoft Excel
* Podívejte se na Blockchain Workbench databázových tabulek a zobrazení
* Načtení Blockchain Workbench zobrazení dat do aplikace Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Připojení k databázi Blockchain Workbench

Připojení k databázi Blockchain Workbench:

1. Otevřete aplikaci Microsoft Excel.
2. Na **Data** , zvolte **načíst Data**.
3. Vyberte **z Azure** a pak vyberte **z Azure SQL Database**.

   ![Připojení k Azure SQL Database](media/blockchain-workbench-data-excel/connect-sql-db.png)

4. V **databáze systému SQL Server** dialogové okno:

    * Pro **Server**, zadejte název serveru Blockchain Workbench.
    * Pro **databáze (volitelné)**, zadejte název databáze.

   ![Zadejte databázový server a databáze](media/blockchain-workbench-data-excel/provide-server-db.png)

5. V **databáze systému SQL Server** navigační panel dialogového okna, vyberte **databáze**. Zadejte vaše **uživatelské jméno** a **heslo**a potom vyberte **Connect**.

    > [!NOTE]
    > Pokud používáte přihlašovací údaje vytvořené během procesu nasazení Azure Blockchain Workbench **uživatelské jméno** je `dbadmin`. **Heslo** je ten, který jste vytvořili při nasazení Blockchain Workbench.
    
   ![Zadejte přihlašovací údaje pro přístup k databázi](media/blockchain-workbench-data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Podívejte se na databáze tabulky a zobrazení

Po připojení k databázi, otevře se dialogové okno aplikace Excel Navigátor. Navigátoru můžete se podívat na tabulky a zobrazení v databázi. Zobrazení jsou navrženy pro vytváření sestav a jejich názvy mají předponu **zobrazit**.

   ![Navigátor Excel preview zobrazení](media/blockchain-workbench-data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Načtení zobrazení dat do sešitu aplikace Excel

Další příklad ukazuje, jak načíst data ze zobrazení do sešitu aplikace Excel.

1. V **Navigátor** posuvníku, vyberte **vwContractAction** zobrazení. **VwContractAction** náhled zobrazuje všechny akce související s kontraktu v databázi Blockchain Workbench.
2. Vyberte **zatížení** načíst všechna data v zobrazení a umístí jej v sešitu aplikace Excel.

   ![Data načtená z zobrazení](media/blockchain-workbench-data-excel/view-data.png)

Teď, když máte data načíst, slouží k vytvoření vlastních sestav pomocí metadat a transakce data z databáze Azure Blockchain Workbench funkce aplikace Excel.

## <a name="next-steps"></a>Další postup

* [Zobrazení databáze v Azure Blockchain Workbench](blockchain-workbench-database-views.md)