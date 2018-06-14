---
title: Obnovení Azure SQL Data Warehouse (portál Azure) | Microsoft Docs
description: Azure portálu úlohy pro obnovení Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6268f500d44bf7ceaaccbcc8e2c2134b42032197
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526868"
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Obnovení Azure SQL Data Warehouse (portál)
> [!div class="op_single_selector"]
> * [Přehled][Overview]
> * [Portál][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
>
>
V tomto článku se dozvíte, jak obnovit Azure SQL Data Warehouse pomocí portálu Azure.

## <a name="before-you-begin"></a>Než začnete
**Ověření vaší DTU kapacity.** Každá instance služby SQL Data Warehouse je hostitelem SQL serveru (například myserver.database.windows.net), který má výchozí kvótu jednotek (DTU) propustnost data. Před obnovením SQL Data Warehouse, ověřte, zda SQL server má dostatek zbývající kvóty DTU pro databázi, kterou jste obnovení. Informace o výpočtu kvóty DTU nebo požádejte o další Dtu najdete v tématu [žádosti o změnu kvóty DTU][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Obnovit databázi active nebo pozastavena
Chcete-li obnovit databázi:

1. Přihlaste se na web [Azure Portal][Azure portal].
2. V levém podokně vyberte **Procházet**a potom vyberte **servery SQL**.

    ![Vyberte Procházet > servery SQL Server](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Najít váš server a pak ho vyberte.

    ![Vyberte svůj server](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Najít instanci služby SQL Data Warehouse, kterou chcete obnovit z a pak ho vyberte.

    ![Vyberte instanci služby SQL Data Warehouse k obnovení](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. V horní části okna datového skladu, vyberte **obnovení**.

    ![Vyberte možnost obnovení](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Zadejte novou **název databáze**.
7. Vyberte nejnovější **bod obnovení**.

   Ujistěte se, že vyberete nejnovější bod obnovení. Protože body obnovení jsou uvedeny v koordinovaný světový čas (UTC), nemusí být výchozí možnost nejnovější bod obnovení.

      ![Vyberte bod obnovení](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Vyberte **OK**.
9. Bude zahájena v procesu obnovení databáze a můžete použít **oznámení** k monitorování procesu.

> [!NOTE]
> Po dokončení obnovení můžete nakonfigurovat obnovené databáze pomocí následujících [nakonfigurovat databázi po obnovení][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Obnovení odstraněné databáze
Chcete-li obnovit odstraněnou databázi:

1. Přihlaste se na web [Azure Portal][Azure portal].
2. V levém podokně vyberte **Procházet**a potom vyberte **servery SQL**.

    ![Vyberte Procházet > servery SQL Server](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Najít váš server a pak ho vyberte.

    ![Vyberte svůj server](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Přejděte dolů k položce **Operations** část v okně vašeho serveru.
5. Vyberte **odstranila databáze** dlaždici.

    ![Vyberte dlaždici odstraněné databáze](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Vyberte, kterou chcete obnovit odstraněnou databázi.

    ![Vyberte databázi pro obnovení](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Zadejte novou **název databáze**.

    ![Přidat název databáze](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Vyberte **OK**.
9. Bude zahájena v procesu obnovení databáze a můžete použít **oznámení** k monitorování procesu.

> [!NOTE]
> Konfigurace databáze po dokončení obnovení najdete v tématu [nakonfigurovat databázi po obnovení][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Další postup
Další informace o funkcích kontinuity obchodních edice Azure SQL Database, přečtěte si [Azure SQL Database obchodní kontinuity přehled][Azure SQL Database business continuity overview].

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
