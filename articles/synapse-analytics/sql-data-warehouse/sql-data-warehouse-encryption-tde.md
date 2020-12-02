---
title: Transparentní šifrování dat (portál) pro vyhrazený fond SQL (dříve SQL DW)
description: Transparentní šifrování dat (TDE) pro vyhrazený fond SQL (dříve SQL DW) ve službě Azure synapse Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 45c7b53d3bbe0c57e96fc5435650c4e86b0cb032
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455261"
---
# <a name="get-started-with-transparent-data-encryption-tde-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Začínáme s transparentní šifrování dat (TDE) pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics

> [!div class="op_single_selector"]
>
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Authentication](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Požadovaná oprávnění

Pokud chcete povolit transparentní šifrování dat (TDE), musíte být správcem nebo členem role dbmanager.

## <a name="enabling-encryption"></a>Povolení šifrování

Pokud chcete povolit TDE, postupujte podle následujících kroků:

1. Otevřete databázi v [Azure Portal](https://portal.azure.com)
2. V okně databáze klikněte na tlačítko **Nastavení** .
3. Vyberte nastavení portálu možnosti **šifrování transparentního šifrování dat** . ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Vyberte nastavení **On** ![ nastavení portálu zapnuto.](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Vybrat **Uložit** 
    ![ nastavení portálu Uložit](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Zakázání šifrování

Pokud chcete zakázat TDE, postupujte podle následujících kroků:

1. Otevřete databázi v [Azure Portal](https://portal.azure.com)
2. V okně databáze klikněte na tlačítko **Nastavení** .
3. Vyberte nastavení portálu možnosti **šifrování transparentního šifrování dat** . ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Vyberte **vypnout** ![ nastavení portálu.](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Vyberte **Uložit** 
    ![ nastavení portálu uložit 2.](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Zobrazení dynamické správy šifrování

Šifrování lze potvrdit pomocí následujících zobrazení dynamické správy:

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
