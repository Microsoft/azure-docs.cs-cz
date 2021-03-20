---
title: Dotazování dat Azure blockchain Workbench pomocí SQL Server Management Studio
description: Zjistěte, jak se připojit k databázi SQL Database služby Azure Blockchain Workbench z aplikace SQL Server Management Studio.
ms.date: 11/20/2019
ms.topic: how-to
ms.service: azure-blockchain
ms.reviewer: mmercuri
ms.openlocfilehash: 16e7f9a6c36ea42e1d0a4144e680baebee5a6c21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009462"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Použití dat služby Azure Blockchain Workbench v aplikaci SQL Server Management Studio

Microsoft SQL Server Management Studio poskytuje možnost rychlého psaní a testování dotazů na databázi SQL Azure blockchain Workbench. V této části najdete podrobný návod k tomu, jak se připojit k SQL Database Azure blockchain Workbench z SQL Server Management Studio.

## <a name="prerequisites"></a>Předpoklady

* Stáhněte si [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Připojení aplikace SQL Server Management Studio k datům ve službě Azure Blockchain Workbench

1. Otevřete aplikaci SQL Server Management Studio a vyberte **Připojit**.
2. Vyberte **Databázový stroj**.

    ![Databázový stroj](./media/data-sql-management-studio/database-engine.png)

3. Do dialogového okna **Připojení k serveru** zadejte název serveru a přihlašovací údaje k databázi.

    Pokud používáte přihlašovací údaje vytvořené při nasazení služby Azure Blockchain Workbench, pak uživatelským jménem je **dbadmin** a heslo je stejné jako to, které jste při zadali nasazení.

    ![Zadání přihlašovacích údajů SQL](./media/data-sql-management-studio/sql-creds.png)

   1. Aplikace SQL Server Management Studio zobrazí v databázi služby Azure Blockchain Workbench seznam databází, zobrazení databáze a uložené procedury.

      ![Seznam databází](./media/data-sql-management-studio/db-list.png)

5. Pokud chcete zobrazit data související s libovolným zobrazením databáze, můžete pomocí následujících kroků automaticky vygenerovat a vybrat příkaz.
6. Klikněte pravým tlačítkem na libovolné zobrazení databáze v Průzkumník objektů.
7. Vyberte **Script View as** (Zobrazit skript jako).
8. Zvolte **SELECT to** (VYBRAT do).
9. Vyberte **New Query Editor Window** (Nové okno Editoru dotazů).
10. Vybráním možnosti **New Query** (Nový dotaz) vytvoříte nový dotaz.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazení databáze ve službě Azure Blockchain Workbench](database-views.md)
