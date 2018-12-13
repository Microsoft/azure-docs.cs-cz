---
title: Použití dat služby Azure Blockchain Workbench v aplikaci SQL Server Management Studio
description: Zjistěte, jak se připojit k databázi SQL Database služby Azure Blockchain Workbench z aplikace SQL Server Management Studio.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 3a0b4db77e6dfb8be3a2f943052925001e5f7715
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53259779"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Použití dat služby Azure Blockchain Workbench v aplikaci SQL Server Management Studio

Microsoft SQL Server Management Studio umožňuje rychlé psaní a testování dotazů na databázi SQL Azure Blockchain Workbench. Tato část obsahuje podrobný návod, jak se připojit k databázi SQL Database služby Azure Blockchain Workbench z aplikace SQL Server Management Studio.

## <a name="prerequisites"></a>Požadavky

* Stáhnout aplikaci [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Připojení aplikace SQL Server Management Studio k datům ve službě Azure Blockchain Workbench

1. Otevřete aplikaci SQL Server Management Studio a vyberte **Připojit**.
2. Vyberte **Databázový stroj**.

    ![Databázový stroj](./media/data-sql-management-studio/database-engine.png)

3. Do dialogového okna **Připojení k serveru** zadejte název serveru a přihlašovací údaje k databázi.

    Pokud používáte přihlašovací údaje vytvořené při nasazení služby Azure Blockchain Workbench, pak uživatelským jménem bude **dbadmin** a heslo bude stejné jako to, které jste zadali při nasazení.

    ![Zadání přihlašovacích údajů SQL](./media/data-sql-management-studio/sql-creds.png)

 4. Aplikace SQL Server Management Studio zobrazí v databázi služby Azure Blockchain Workbench seznam databází, zobrazení databáze a uložené procedury.

    ![Seznam databází](./media/data-sql-management-studio/db-list.png)

5. Pokud chcete zobrazit data související s libovolným zobrazením databáze, můžete pomocí následujících kroků automaticky vygenerovat a vybrat příkaz.
6. Klikněte v Průzkumníku objektů pravým tlačítkem na libovolné zobrazení databáze.
7. Vyberte **Script View as** (Zobrazit skript jako).
8. Zvolte **SELECT to** (VYBRAT do).
9. Vyberte **New Query Editor Window** (Nové okno Editoru dotazů).
10. Vybráním možnosti **New Query** (Nový dotaz) vytvoříte nový dotaz.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zobrazení databáze ve službě Azure Blockchain Workbench](database-views.md)
