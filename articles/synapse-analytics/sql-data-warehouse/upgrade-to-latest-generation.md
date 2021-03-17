---
title: Upgrade na nejnovější generaci vyhrazeného fondu SQL (dřív SQL DW)
description: Upgradujte vyhrazený fond SQL Azure synapse Analytics (dřív SQL DW) na nejnovější generaci architektury hardwaru a úložiště Azure.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: b5a9d1781bd0498ac6ad74439b1572c52e3c345a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459256"
---
# <a name="optimize-performance-by-upgrading-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Optimalizujte výkon tím, že upgradujete vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics.

Upgradujte vyhrazený fond SQL (dřív SQL DW) na nejnovější generaci architektury hardwaru a úložiště Azure.

## <a name="why-upgrade"></a>Proč upgradovat?

Nyní můžete bez problémů upgradovat na vyhrazený fond SQL (dříve SQL DW) COMPUTE optimalizované pro Gen2 v Azure Portal pro [podporované oblasti](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Pokud vaše oblast nepodporuje vlastní upgrade, můžete upgradovat na podporovanou oblast nebo počkat, až bude vlastní upgrade k dispozici ve vaší oblasti. Upgradujte teď a využijte výhod nejnovější generace hardwaru Azure a rozšířené architektury úložiště, včetně rychlejšího výkonu, vyšší škálovatelnosti a neomezeného sloupcového úložiště.

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

> [!IMPORTANT]
> Tento upgrade se vztahuje na výpočetní fondy SQL vyhrazené Gen1 vrstvy (fornmerly SQL DW) v [podporovaných oblastech](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Než začnete

1. Ověřte, jestli je vaše [oblast](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) podporovaná na migraci Gen1 na Gen2. Poznamenejte si data automatické migrace. Aby nedocházelo ke konfliktům automatizovaného procesu, naplánujte ruční migraci před počátečním datem automatizovaného procesu.
2. Pokud jste v oblasti, která ještě není podporovaná, pokračujte v kontrole oblasti, která se má přidat nebo [upgradovat, pomocí obnovení](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) do podporované oblasti.
3. Pokud je vaše oblast podporovaná, [upgradujte ji pomocí Azure Portal](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Vyberte navrhovanou úroveň výkonu** vyhrazeného fondu SQL (dříve SQL DW) na základě vaší aktuální úrovně výkonu na výpočetní optimalizované úrovni Gen1 pomocí níže uvedeného mapování:

   | Výpočetní optimalizovaná úroveň Gen1 | Výpočetní optimalizovaná úroveň Gen2 |
   | :-------------------------: | :-------------------------: |
   |            OD DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c.            |
   |            ÚROVEŇ DW600            |           DW500c.            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!NOTE]
> Navrhované úrovně výkonu nejsou přímým převodem. Například doporučujeme, abyste provedli z úroveň DW600 na DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Upgrade v podporované oblasti pomocí Azure Portal

- Migrace z Gen1 do Gen2 prostřednictvím Azure Portal je trvalá. Neexistuje proces pro návrat do Gen1.
- Aby bylo možné migrovat na Gen2, musí být spuštěn vyhrazený fond SQL (dřív SQL DW).

### <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
- Ujistěte se, že vyhrazený fond SQL (dřív SQL DW) je spuštěný – musí se migrovat na Gen2.

### <a name="powershell-upgrade-commands"></a>Příkazy upgradu PowerShellu

1. Pokud je Gen1 fond SQL vyhrazený pro výpočetní vrstvu (dřív SQL DW), který se má upgradovat, je pozastaven, [obnovte vyhrazený fond SQL (dřív SQL DW)](pause-and-resume-compute-portal.md).

2. Připravte se na několik minut výpadků.

3. Identifikujte všechny odkazy na výpočetní výkonové úrovně výkonu Gen1 a upravte je na jejich ekvivalentní výpočetní optimalizované úrovni výkonu Gen2. Níže jsou uvedeny dva příklady, kde byste měli aktualizovat odkazy na kód před upgradem:

   Původní příkaz prostředí PowerShell pro Gen1:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Změněno na:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE]
   > -RequestedServiceObjectiveName "DW300" se změnil na-RequestedServiceObjectiveName "DW300 **c**"
   >

   Původní Gen1 příkaz T-SQL:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Změněno na:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ;
   ```

   > [!NOTE]
   > SERVICE_OBJECTIVE = ' DW300 ' se změní na SERVICE_OBJECTIVE = ' DW300 **c**'

## <a name="start-the-upgrade"></a>Spustit upgrade

1. V Azure Portal můžete přejít ke výpočetnímu fondu SQL vyhrazenému pro vyhrazený Gen1 fond SQL (dřív SQL DW). Pokud je Gen1 fond SQL vyhrazený pro výpočetní vrstvu (dříve SQL DW), který se má upgradovat, je pozastaven, [obnovte vyhrazený fond SQL](pause-and-resume-compute-portal.md).
2. Na kartě úlohy vyberte **upgradovat na kartu Gen2** : ![ Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)

   > [!NOTE]
   > Pokud na kartě úkoly nevidíte kartu **upgrade na Gen2** , je váš typ předplatného omezený v aktuální oblasti.
   > [Odešlete lístek podpory](sql-data-warehouse-get-started-create-support-ticket.md) , abyste získali schválení předplatného.

3. Před upgradem zajistěte, aby byla vaše úloha spuštěná a byla nečinná. Během několika minut se dostanou prostoje, než bude vyhrazený fond SQL (dřív SQL DW) zpátky online jako vyhrazený fond SQL vyhrazené Gen2 vrstvy (dřív SQL DW). **Vyberte upgrade**:

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Monitorujte upgrade** tím, že zkontrolujete jeho stav Azure Portal:

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   První krok procesu upgradu projde operace škálování ("upgrade-offline"), kde budou všechny relace ukončeny, a připojení se zahozena.

   Druhým krokem procesu upgradu je migrace dat (upgrade-online). Migrace dat je online proces na pozadí trickle. Tento proces pomalu přesune sloupcová data ze staré architektury úložiště do nové architektury úložiště pomocí místní mezipaměti SSD. Během této doby bude vyhrazený fond SQL (dřív SQL DW) online pro dotazování a načítání. Data budou k dispozici pro dotaz bez ohledu na to, zda byla migrována nebo nikoli. Migrace dat probíhá různě v závislosti na velikosti dat, úrovni výkonu a počtu segmentů columnstore.

5. **Volitelné doporučení:** Po dokončení operace škálování můžete urychlit proces migrace dat na pozadí. Pohyb dat můžete vynutit spuštěním [příkazu ALTER index Rebuild](sql-data-warehouse-tables-index.md) na všech primárních tabulkách columnstore, které se dotazují na větší úrovni objektů slo a prostředků. Tato operace je v porovnání s procesem trickle na pozadí v **režimu offline** , což může trvat hodiny v závislosti na počtu a velikosti tabulek. Nicméně po dokončení bude migrace dat mnohem rychlejší z důvodu nové rozšířené architektury úložiště s vysokou kvalitou rowgroups.

> [!NOTE]
> Příkaz ALTER index Rebuild je offline operace a tabulky nebudou k dispozici, dokud se znovu nedokončí sestavení.

Následující dotaz vygeneruje potřebné příkazy ALTER index rebuild pro urychlení migrace dat:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON ['
       + Schema_name(tbl.schema_id) + '].['
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE
                                                         WHEN (
                                                     (SELECT Count(*)
                                                      FROM   sys.partitions
                                                             part2
                                                      WHERE  part2.index_id
                                                             = idx.index_id
                                                             AND
                                                     idx.object_id =
                                                     part2.object_id)
                                                     > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              ELSE ''
                                                       END ) + '; SELECT ''[' +
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' +
              Object_name(idx.object_id) + '] ' + (
              CASE
                WHEN ( (SELECT Count(*)
                        FROM   sys.partitions
                               part2
                        WHERE
                     part2.index_id =
                     idx.index_id
                     AND idx.object_id
                         = part2.object_id) > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              + ' completed'';'
              ELSE ' completed'';'
                                                    END )
FROM   sys.indexes idx
       INNER JOIN sys.tables tbl
               ON idx.object_id = tbl.object_id
       LEFT OUTER JOIN sys.partitions part
                    ON idx.index_id = part.index_id
                       AND idx.object_id = part.object_id
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE';
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Upgrade z geografické oblasti Azure pomocí obnovení prostřednictvím Azure Portal

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Vytvoření bodu obnovení definovaného uživatelem pomocí Azure Portal

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

2. Přejděte do vyhrazeného fondu SQL (dříve SQL DW), pro který chcete vytvořit bod obnovení.

3. V horní části přehledu vyberte **+ nový bod obnovení**.

    ![Nový bod obnovení](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Zadejte název bodu obnovení.

    ![Název bodu obnovení](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Obnovení aktivní nebo pozastavené databáze pomocí Azure Portal

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Přejděte do vyhrazeného fondu SQL (dříve SQL DW), ze kterého chcete obnovit.
3. V horní části přehledu vyberte **obnovit**.

    ![ Obnovení – přehled](./media/upgrade-to-latest-generation/restoring_0.png)

4. Vyberte buď **body automatického obnovení** nebo **body obnovení definované uživatelem**. V případě uživatelem definovaných bodů obnovení **Vyberte uživatelem definovaný bod obnovení** nebo **vytvořte nový bod obnovení definovaný uživatelem**. Pro server vyberte **vytvořit novou** a zvolte server v oblasti podporované v Gen2.

    ![Automatické body obnovení](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Obnovení z geografické oblasti Azure pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

K obnovení databáze použijte rutinu [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> K Gen2 můžete použít geografické obnovení. Provedete to tak, že jako volitelný parametr zadáte Gen2 ServiceObjectiveName (např. DW1000 **c**).

1. Otevřete Windows PowerShell.
2. Připojte se k účtu Azure a seznamte se se všemi předplatnými přidruženými k vašemu účtu.
3. Vyberte předplatné, které obsahuje databázi, kterou chcete obnovit.
4. Získejte databázi, kterou chcete obnovit.
5. Vytvořte žádost o obnovení pro databázi a určete Gen2 ServiceObjectiveName.
6. Ověřte stav geograficky obnovené databáze.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Konfiguraci databáze po dokončení obnovení najdete v tématu [Konfigurace databáze po obnovení](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

Pokud je zdrojová databáze povolena TDE, obnovená databáze bude TDE povolena.

Pokud dojde k potížím s vyhrazeným fondem SQL, vytvořte [žádost o podporu](sql-data-warehouse-get-started-create-support-ticket.md) a odkaz "Gen2 upgrade" jako možnou příčinu.

## <a name="next-steps"></a>Další kroky

Upgradovaný vyhrazený fond SQL (dřív SQL DW) je online. Pokud chcete využít vylepšenou architekturu, přečtěte si téma [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).
