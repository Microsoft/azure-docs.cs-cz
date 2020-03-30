---
title: Upgrade na nejnovější generaci
description: Upgradujte fond SQL Azure Synapse Analytics na nejnovější generaci architektury hardwaru a úložiště Azure.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 637e377e469eeb1a82b6c0ad3a845d94ac09c7db
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351196"
---
# <a name="optimize-performance-by-upgrading-azure-synapse-analytics-sql-pool"></a>Optimalizace výkonu upgradem fondu SQL Azure Synapse Analytics

Upgradujte fond SQL na nejnovější generaci architektury hardwaru a úložiště Azure.

## <a name="why-upgrade"></a>Proč upgradovat?

Teď můžete bez problémů upgradovat na úroveň SQL pool Compute Optimized Gen2 na webu Azure Portal pro [podporované oblasti](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Pokud vaše oblast nepodporuje vlastní upgrade, můžete upgradovat na podporovanou oblast nebo počkat, až bude ve vaší oblasti k dispozici vlastní upgrade. Upgradujte nyní, abyste využili nejnovější generaci hardwaru Azure a vylepšené architektury úložiště, včetně vyššího výkonu, vyšší škálovatelnosti a neomezeného sloupcového úložiště. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Platí pro

Tento upgrade se vztahuje na fondy SQL úrovně SQL optimalizované pro výpočetní výkony v [podporovaných oblastech](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Než začnete

1. Zkontrolujte, zda je vaše [oblast](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) podporována pro migraci GEN1 na GEN2. Poznamenejte si data automatické migrace. Chcete-li se vyhnout konfliktům s automatizovaným procesem, naplánujte ruční migraci před datem zahájení automatizovaného procesu.
2. Pokud se nacházíte v oblasti, která ještě není podporována, pokračujte v kontrole, zda má být oblast přidána, nebo [upgradujte pomocí obnovení](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) do podporované oblasti.
3. Pokud je vaše oblast podporovaná, [upgradujte na webu Azure Portal.](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Vyberte navrhovanou úroveň výkonu** pro fond SQL na základě aktuální úrovně výkonu na úrovni Gen1 optimalizované pro výpočetní výkon pomocí níže uvedeného mapování:

   | Výpočetní optimalizovaná úroveň Gen1 | Výpočetní optimalizovaná úroveň Gen2 |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c.            |
   |            DW600            |           DW500c.            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!Note]
> Navrhované úrovně výkonu nejsou přímou konverzí. Například doporučujeme jít z DW600 do DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Upgrade v podporované oblasti pomocí portálu Azure

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Migrace z Gen1 na Gen2 prostřednictvím portálu Azure je trvalá. Neexistuje žádný proces pro návrat do Gen1.  

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Pokud výpočetní optimalizované Gen1 vrstvy SQL fond, který má být pozastaven, je pozastaveno, [pokračovat ve fondu SQL](pause-and-resume-compute-portal.md).

   > [!NOTE]
   > Fond SQL musí být spuštěn, aby se migroval do Gen2.

2. Buďte připraveni na několik minut prostojů. 

3. Identifikujte všechny odkazy kódu na úrovně výkonu Gen1 optimalizované pro výpočetní výkon a upravte je na ekvivalentní úroveň výkonu Gen2 optimalizovaná pro výpočetní výkon. Níže jsou uvedeny dva příklady, kde byste měli aktualizovat odkazy na kód před upgradem:

   Původní gen1 PowerShell, příkaz:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Upraveno takto:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" se změní na - RequestedServiceObjectiveName "DW300**c**"
   >

   Původní příkaz Gen1 T-SQL:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Upraveno takto:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = "DW300" se změní na SERVICE_OBJECTIVE = 'DW300**c**'

## <a name="start-the-upgrade"></a>Spuštění upgradu

1. Přejděte do fondu SQL optimalizovaného pro výpočetní výkony na webu Azure Portal. Pokud výpočetní optimalizované Gen1 vrstvy SQL fond, který má být pozastaven, je pozastaveno, [pokračovat ve fondu SQL](pause-and-resume-compute-portal.md). 
2. Na kartě Úkoly vyberte ![Upgradovat na kartu **Gen2:** Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)
    
    > [!NOTE]
    > Pokud kartu Upgrade **na Gen2** nevidíte na kartě Úkoly, je typ předplatného v aktuální oblasti omezený.
    > [Odešlete lístek podpory,](sql-data-warehouse-get-started-create-support-ticket.md) abyste získali své předplatné na seznamu povolených.

3. Ujistěte se, že vaše úloha byla dokončena spuštění a nefungování před upgradem. Pokud se několik minut objeví prostoje, než bude váš fond SQL zase online jako fond SQL úrovně Gen2 optimalizované pro výpočetní výkony. **Vyberte Možnost Inovovat**:

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Sledujte upgrade** kontrolou stavu na webu Azure Portal:

   ![Inovace3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   První krok procesu upgradu prochází operace škálování ("Upgrade - Offline"), kde budou všechny relace vyřazeny a připojení budou zrušena. 

   Druhým krokem procesu upgradu je migrace dat ("Upgrade – online"). Migrace dat je online proces na pozadí. Tento proces pomalu přesouvá sloupcová data ze staré architektury úložiště do nové architektury úložiště pomocí místní mezipaměti SSD. Během této doby bude fond SQL online pro dotazování a načítání. Data budou k dispozici pro dotaz bez ohledu na to, zda byla migrována či nikoli. Migrace dat probíhá různou rychlostí v závislosti na velikosti dat, úrovni výkonu a počtu segmentů columnstore. 

5. **Volitelné doporučení:** Po dokončení operace škálování můžete urychlit proces migrace dat na pozadí. Přesun dat můžete vynutit spuštěním [obnovení indexu Alter](sql-data-warehouse-tables-index.md) ve všech primárních tabulkách columnstore, na které byste se dotazovali ve větší třídě SLO a na třídu prostředků. Tato operace je **offline** ve srovnání s procesem stékání pozadí, což může trvat hodiny dokončit v závislosti na počtu a velikosti tabulek. Po dokončení však bude migrace dat mnohem rychlejší díky nové rozšířené architektuře úložiště s vysoce kvalitními skupinami řádků.
 
> [!NOTE]
> Alter Index znovu sestavit je operace offline a tabulky nebudou k dispozici, dokud znovu sestavit dokončí.

Následující dotaz generuje požadované příkazy alter index znovu sestavit pro urychlení migrace dat:

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

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Upgrade ze zeměpisné oblasti Azure pomocí obnovení prostřednictvím portálu Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Vytvoření uživatelem definovaného bodu obnovení pomocí portálu Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Přejděte do fondu SQL, pro který chcete vytvořit bod obnovení.

3. V horní části části Přehled vyberte **+Nový bod obnovení**.

    ![Nový bod obnovení](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Zadejte název bodu obnovení.

    ![Název bodu obnovení](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Obnovení aktivní nebo pozastavené databáze pomocí webu Azure Portal

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Přejděte do fondu SQL, ze kterého chcete obnovit.
3. V horní části části Přehled vyberte **Obnovit**.

    ![ Obnovení – přehled](./media/upgrade-to-latest-generation/restoring_0.png)

4. Vyberte buď **automatické body obnovení,** nebo **uživatelem definované body obnovení**. Pro uživatelem definované body obnovení **vyberte uživatelem definovaný bod obnovení** nebo **Vytvořit nový uživatelem definovaný bod obnovení**. Pro server vyberte **Vytvořit nový** a zvolte server v geografické oblasti podporované Gen2. 

    ![Automatické body obnovení](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Obnovení ze zeměpisné oblasti Azure pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Chcete-li obnovit databázi, použijte rutinu [Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase)

> [!NOTE]
> Můžete provést geo-obnovení Gen2! Chcete-li tak učinit, zadejte Gen2 ServiceObjectiveName (např. DW1000**c**) jako volitelný parametr.

1. Otevřete Windows PowerShell.
2. Připojte se ke svému účtu Azure a uveďte všechna předplatná přidružená k vašemu účtu.
3. Vyberte odběr, který obsahuje databázi, která má být obnovena.
4. Získejte databázi, kterou chcete obnovit.
5. Vytvořte požadavek na obnovení databáze a zadejte gen2 ServiceObjectiveName.
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
> Informace o konfiguraci databáze po dokončení obnovení naleznete v [tématu Konfigurace databáze po obnovení](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

Obnovená databáze bude povolena tde, pokud je zdrojová databáze povolena TDE.


Pokud narazíte na nějaké problémy s fondem SQL, vytvořte [žádost o podporu](sql-data-warehouse-get-started-create-support-ticket.md) a odkazovat na "Gen2 upgrade" jako možnou příčinu.

## <a name="next-steps"></a>Další kroky

Upgradovaný fond SQL je online. Chcete-li využít výhod rozšířené architektury, naleznete [v tématu třídy prostředků pro správu pracovních vytížení](resource-classes-for-workload-management.md).
