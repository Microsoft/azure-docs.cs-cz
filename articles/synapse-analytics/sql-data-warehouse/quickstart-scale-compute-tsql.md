---
title: 'Rychlý Start: škálování výpočetních prostředků ve vyhrazeném fondu SQL (dřív SQL DW) – T-SQL'
description: Škálovat výpočetní prostředky ve vyhrazeném fondu SQL (dřív SQL DW) pomocí T-SQL a SQL Server Management Studio (SSMS). Kapacitu výpočetních prostředků můžete horizontálně navýšit, abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f3eaa6383b174e06ff6ce9b1b08c4eba9108d770
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115510"
---
# <a name="quickstart-scale-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-t-sql"></a>Rychlý Start: škálování COMPUTE pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics pomocí T-SQL

Škálovat výpočetní prostředky ve vyhrazeném fondu SQL (dřív SQL DW) pomocí T-SQL a SQL Server Management Studio (SSMS). Kapacitu výpočetních prostředků můžete [horizontálně navýšit](sql-data-warehouse-manage-compute-overview.md), abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete

Stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Vytvoření vyhrazeného fondu SQL (dřív SQL DW)

Použijte [rychlý Start: vytvoření a připojení – portál](create-data-warehouse-portal.md) pro vytvoření vyhrazeného fondu SQL (dřív SQL DW) s názvem **mySampleDataWarehouse**. Dokončete rychlý Start, abyste měli jistotu, že máte pravidlo brány firewall a můžete se připojit k vašemu vyhrazenému fondu SQL (dřív SQL DW) z SQL Server Management Studio.

## <a name="connect-to-the-server-as-server-admin"></a>Připojení k serveru jako správce serveru

V této části se pomocí aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) naváže připojení k serveru SQL Azure.

1. Otevřete sadu SQL Server Management Studio.

2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | Typ serveru | Databázový stroj | Tato hodnota se vyžaduje. |
   | Název serveru | Plně kvalifikovaný název serveru | Tady je příklad: **mySampleDataWarehouseservername.Database.Windows.NET**. |
   | Ověřování | Ověřování SQL Serveru | Ověřování SQL je jediný typ ověřování, který se v tomto kurzu konfiguruje. |
   | Přihlásit | Účet správce serveru | Jedná se o účet, který jste zadali při vytváření serveru. |
   | Heslo | Heslo pro účet správce serveru | Heslo, které jste zadali při vytváření serveru. |

    ![Připojení k serveru](./media/quickstart-scale-compute-tsql/connect-to-server.png)

3. Klikněte na **Připojit**. V aplikaci SSMS se otevře okno Průzkumníka objektů.

4. V Průzkumníku objektů rozbalte **Databáze**. Pak rozbalte **mySampleDataWarehouse** a zobrazte objekty v nové databázi.

    ![Objekty databáze](./media/quickstart-scale-compute-tsql/connected.png)

## <a name="view-service-objective"></a>Zobrazení cíle služby

Nastavení cíle služby obsahuje počet jednotek datového skladu pro vyhrazený fond SQL (dřív SQL DW).

Zobrazení aktuálních jednotek datového skladu pro vyhrazený fond SQL (dříve SQL DW):

1. V části připojení k **mySampleDataWarehouseservername.Database.Windows.NET** rozbalte **systémové databáze**.
2. Klikněte pravým tlačítkem na **master** a vyberte **New Query** (Nový dotaz). Otevře se nové okno dotazu.
3. Spusťte následující dotaz pro výběr ze zobrazení dynamické správy sys.database_service_objectives.

    ```sql
    SELECT
        db.name [Database]
    ,    ds.edition [Edition]
    ,    ds.service_objective [Service Objective]
    FROM
         sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE
        db.name = 'mySampleDataWarehouse'
    ```

4. Z následujících výsledků je zřejmé, že **mySampleDataWarehouse** má jako cíl služby DW400.

    ![inobrazování – aktuální – DWU](./media/quickstart-scale-compute-tsql/view-current-dwu.png)

## <a name="scale-compute"></a>Škálování výpočetního výkonu

Ve vyhrazeném fondu SQL (dříve SQL DW) můžete zvýšit nebo snížit výpočetní prostředky tím, že upravíte jednotky datového skladu. Podle postupu v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) jste vytvořili **mySampleDataWarehouse** a inicializovali ho se 400 jednotkami datového skladu. V následujícím postupu upravíte jednotky datového skladu pro **mySampleDataWarehouse**.

Změna jednotek datového skladu:

1. Klikněte pravým tlačítkem na **master** a vyberte **New Query** (Nový dotaz).
2. Ke změně cíle služby použijte příkaz T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Spusťte následující dotaz a změňte cíl služby na DW300.

    ```Sql
    ALTER DATABASE mySampleDataWarehouse
    MODIFY (SERVICE_OBJECTIVE = 'DW300c');
    ```

## <a name="monitor-scale-change-request"></a>Monitorování žádostí o změnu rozsahu

K zobrazení průběhu předchozí žádosti o změnu můžete použít syntaxi T-SQL `WAITFORDELAY` a dotázat se na stav dynamického zobrazení správy (DMV) sys.dm_operation_status.

Pokud se chcete dotázat na stav změny objektu služby:

1. Klikněte pravým tlačítkem na **master** a vyberte **New Query** (Nový dotaz).
2. Spuštěním následujícího dotazu se dotážete na stav DMV sys.dm_operation_status.

    ```sql
    WHILE
    (
        SELECT TOP 1 state_desc
        FROM sys.dm_operation_status
        WHERE
            1=1
            AND resource_type_desc = 'Database'
            AND major_resource_id = 'mySampleDataWarehouse'
            AND operation = 'ALTER DATABASE'
        ORDER BY
            start_time DESC
    ) = 'IN_PROGRESS'
    BEGIN
        RAISERROR('Scale operation in progress',0,0) WITH NOWAIT;
        WAITFOR DELAY '00:00:05';
    END
    PRINT 'Complete';
    ```

3. Výsledný výstup zobrazuje protokol dotazování stavu.

    ![Stav operace](./media/quickstart-scale-compute-tsql/polling-output.png)

## <a name="check-dedicated-sql-pool-formerly-sql-dw-state"></a>Ověřte stav vyhrazeného fondu SQL (dříve SQL DW).

Pokud je vyhrazený fond SQL (dřív SQL DW) pozastaven, nemůžete se k němu připojit pomocí T-SQL. Pokud chcete zobrazit aktuální stav vyhrazeného fondu SQL (dřív SQL DW), můžete použít rutinu PowerShellu. Příklad najdete v tématu o [kontrole vyhrazeného fondu SQL (dříve SQL DW) stavu – PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state).

## <a name="check-operation-status"></a>Kontrola stavu operace

Pokud chcete vracet informace o různých operacích správy ve vyhrazeném fondu SQL (dřív SQL DW), spusťte na [Sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) DMV následující dotaz. Vrátí se například operace a její stav, který bude IN_PROGRESS nebo COMPLETED.

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND
    major_resource_id = 'mySampleDataWarehouse'
```

## <a name="next-steps"></a>Další kroky

Nyní jste zjistili, jak škálovat výpočetní prostředky pro vyhrazený fond SQL (dřív SQL DW). Další informace o službě Azure synapse Analytics najdete v kurzu načtení dat.

> [!div class="nextstepaction"]
>[Načtení dat do vyhrazeného fondu SQL](./load-data-from-azure-blob-storage-using-copy.md)