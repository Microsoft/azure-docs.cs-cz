---
title: Obnovení Azure SQL Data Warehouse (Azure portal) | Dokumentace Microsoftu
description: Azure portal úkoly pro obnovení Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 0d8bb451c821a4883c81e0e51c5d98e146e3008e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243534"
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Obnovení Azure SQL Data Warehouse (portál)
> [!div class="op_single_selector"]
> * [Přehled][Overview]
> * [Portál][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
>
>
V tomto článku se dozvíte, jak obnovit Azure SQL Data Warehouse s využitím webu Azure portal.

## <a name="before-you-begin"></a>Před zahájením
**Ověřte kapacitu jednotek DTU.** Každá instance služby SQL Data Warehouse je hostitelem SQL serveru (např. myserver.database.windows.net), který má výchozí kvóta jednotek (DTU) propustnost data. Předtím, než bude možné obnovit SQL Data Warehouse, ověřte, zda má váš server SQL dostatečně zbývající kvóta DTU pro databázi, kterou jste obnovení. Zjistěte, jak vypočítat kvóty DTU nebo požádejte o další Dtu, najdete v článku [žádost o změnu kvóty DTU][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Obnovit databázi aktivní nebo pozastavena
Chcete-li obnovit databázi:

1. Přihlaste se na web [Azure Portal][Azure portal].
2. V levém podokně vyberte **Procházet**a pak vyberte **SQL servery**.

    ![Kliknout na Procházet > servery SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Vyhledejte váš server a pak vyberte ji.

    ![Vyberte svůj server](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Najít instanci služby SQL Data Warehouse, kterou chcete obnovit z a vyberte ji.

    ![Vyberte instanci služby SQL Data Warehouse k obnovení](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. V horní části okna datového skladu, vyberte **obnovení**.

    ![Vyberte obnovení](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Zadejte nový **název_databáze**.
7. Vyberte nejnovější **bod obnovení**.

   Ujistěte se, že vyberete nejnovější bod obnovení. Vzhledem k tomu, že se body obnovení jsou uvedeny v koordinovaný univerzální čas (UTC), nemusí být výchozí volba nejnovější bod obnovení.

      ![Vyberte bod obnovení](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Vyberte **OK**.
9. Spustí proces obnovení databáze, můžete si **oznámení** monitorování procesu.

> [!NOTE]
> Po dokončení obnovení můžete nakonfigurovat obnovené databáze pomocí následujících [nakonfigurovat svou databázi po obnovení][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Obnovení odstraněné databáze
Chcete-li obnovit odstraněnou databázi:

1. Přihlaste se na web [Azure Portal][Azure portal].
2. V levém podokně vyberte **Procházet**a pak vyberte **SQL servery**.

    ![Kliknout na Procházet > servery SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Vyhledejte váš server a pak vyberte ji.

    ![Vyberte svůj server](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Přejděte dolů k položce **operace** části v okně vašeho serveru.
5. Vyberte **odstranila databáze** dlaždici.

    ![Vyberte dlaždici odstraněné databáze](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Vyberte, kterou chcete obnovit odstraněnou databázi.

    ![Vyberte databázi pro obnovení](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Zadejte nový **název_databáze**.

    ![Přidat název databáze](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Vyberte **OK**.
9. Spustí proces obnovení databáze, můžete si **oznámení** monitorování procesu.

> [!NOTE]
> Po dokončení obnovení konfigurace vaší databáze, najdete v článku [nakonfigurovat svou databázi po obnovení][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Další postup
Další informace o obchodní kontinuity podnikových procesů funkce edice Azure SQL Database, přečtěte si [přehled zajištění provozní kontinuity podnikání Azure SQL Database][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
