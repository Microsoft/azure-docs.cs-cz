---
title: Pomocí Azure Blockchain Workbench dat SQL Server Management Studio
description: Zjistěte, jak se připojit k databázi SQL Azure Blockchain Workbench v rámci SQL Server Management Studio.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 1c9ee1e94ba1195db027c3edfab2e2a96f181ca3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Pomocí Azure Blockchain Workbench dat SQL Server Management Studio

Microsoft SQL Server Management Studio poskytuje možnost rychle psaní a testování dotazy na databázi SQL Azure Blockhain Workbench. Tato část obsahuje podrobný návod, jak se připojit k databázi SQL Azure Blockchain Workbench v rámci SQL Server Management Studio.

## <a name="prerequisites"></a>Požadavky

* Stáhněte si [SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Připojování k datům v Azure Blockchain Workbench SQL Server Management Studio

1. Otevřete SQL Server Management Studio a vyberte **Connect**.
2. Vyberte **databáze modul**.

    ![Databázový stroj](media/blockchain-workbench-data-sql-management-studio/database-engine.png)

3. V **připojit k serveru** dialogové okno, zadejte název serveru a přihlašovací údaje vaší databáze.

    Pokud používáte přihlašovací údaje vytvořit proces nasazení Azure Blockchain Workbench, uživatelské jméno bude **dbadmin** a heslo bude dodaná během nasazení.

    ![Zadejte přihlašovací údaje SQL](media/blockchain-workbench-data-sql-management-studio/sql-creds.png)

 4. SQL Server Management Studio zobrazí seznam databáze, databáze zobrazení a uložených procedur v databázi Azure Blockchain Workbench.

    ![Seznam databází](media/blockchain-workbench-data-sql-management-studio/db-list.png)

5. Chcete-li zobrazit data související s žádným z zobrazení databáze, můžete automaticky generovat příkaz select pomocí následujících kroků.
6. Klikněte pravým tlačítkem na některém ze zobrazení databáze v Průzkumníku objektů.
7. Vyberte **skript zobrazení jako**.
8. Zvolte **vybrat**.
9. Vyberte **nové okno editoru dotazů**.
10. Lze vytvořit nový dotaz tak, že vyberete **nový dotaz**.

## <a name="next-steps"></a>Další postup

* [Zobrazení databáze v Azure Blockchain Workbench](blockchain-workbench-database-views.md)