---
title: Upgrade na nejnovější generace služby Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Upgradujte na nejnovější generace architektury Azure hardware a úložiště Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.openlocfilehash: f3e877733d473993a5acd2f44e088b8b0b4fe130
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447255"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optimalizace výkonu díky upgradu SQL Data Warehouse
Upgradujte na nejnovější generace architektury Azure hardware a úložiště Azure SQL Data Warehouse.

## <a name="why-upgrade"></a>Proč upgradovat?
Nově můžete bez problémů upgradovat na úroveň SQL Data Warehouse – Compute optimalizované Gen2 na webu Azure Portal pro [podporované oblasti](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Pokud vaše oblast není podporován místním upgradu, můžete upgradovat na podporovanou oblast nebo počkejte místním upgradu k dispozici ve vaší oblasti. Upgradujte, abyste mohli využívat nejnovější generace hardwaru Azure a rozšířené úložiště architekturu, včetně vyšší výkon, vyšší škálovatelnost a neomezené sloupcové úložiště. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Platná pro
Tento upgrade se vztahuje na – Compute optimalizované Gen1 úroveň datové sklady v [podporované oblasti](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Před zahájením

1. Zkontrolujte, jestli vaše [oblasti](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) se podporuje pro GEN1 GEN2 migrace. Poznamenejte si data automatickou migraci. Aby nedocházelo ke konfliktům s automatizovaným procesem, naplánujte si ruční migraci před datem zahájení automatizovaného procesu.
2. Pokud jste v oblasti, která zatím není podporovaná, pokračovat ke kontrole vaší oblasti, které mají být přidány nebo [upgrade pomocí obnovení](#Upgrade-from-an-Azure-geographical-region-using-restore-through-the-Azure-portal) v podporované oblasti.
3. Pokud se vaší oblasti podporuje, [upgrade prostřednictvím webu Azure portal](#Upgrade-in-a-supported-region-using-the-Azure-portal)
4. **Vyberte úroveň výkonu navrhované** pro datový sklad na základě vaší aktuální úroveň výkonu na úrovni Gen1 – Compute optimalizované pomocí následující mapování:

   | Služba COMPUTE úrovně optimalizované Gen1 | Služba COMPUTE úrovně optimalizované Gen2 |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |
>[!Note]
>Úrovně výkonu navrhované nejsou přímý převod. Například doporučujeme přejít z DW600 DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Upgrade v podporované oblasti pomocí webu Azure portal

> [!NOTE]
> Migrace z GEN1 na GEN2 prostřednictvím webu Azure portal je trvalá. Proces pro vrácení GEN1 není.  

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Pokud datový sklad vrstvy – Compute optimalizované Gen1 k upgradu je pozastavený, [obnovit datový sklad](pause-and-resume-compute-portal.md).

   > [!NOTE]
   > Azure SQL Data Warehouse, musí být spuštěna pro migraci na Gen2.

2. Připravit na pár minut prostojů. 

3. Určit všechny reference na části kódu do úrovní výkonu – Compute optimalizované Gen1 a na jejich odpovídající úroveň výkonu – Compute optimalizované Gen2 je upravit. Níže jsou uvedeny dva příklady z které by měl aktualizovat odkazy na kód před upgradem:

   Původní příkaz Gen1 Powershellu:

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Upravit tak, aby:

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" se změní na - RequestedServiceObjectiveName "DW300**c**"
   >

   Původní příkaz Gen1 T-SQL:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Upravit tak, aby:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > Parametr SERVICE_OBJECTIVE = "DW300" se změní na SERVICE_OBJECTIVE = "DW300**c**.



## <a name="start-the-upgrade"></a>Spuštění upgradu

1. Přejdete na úroveň – Compute optimalizované Gen1 datového skladu na webu Azure Portal. Pokud datový sklad vrstvy – Compute optimalizované Gen1 k upgradu je pozastavený, [obnovit datový sklad](pause-and-resume-compute-portal.md). 
2. Vyberte **upgradovat na Gen2** karty na kartě úlohy:  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > Pokud se nezobrazí **upgradovat na Gen2** karet na kartě úlohy, typ vašeho předplatného je omezený v aktuální oblasti.
    > [Vyplňte lístek podpory](sql-data-warehouse-get-started-create-support-ticket.md) získat vaše předplatné povolený.


3. Zajistěte, aby že úloha byla dokončena spuštění a nečinná před upgradem. Pro několik minut, než váš datový sklad je zpátky do online režimu jako datový sklad vrstvy – Compute optimalizované Gen2 je budete setkávat s výpadky. **Vyberte Upgrade**:

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitorování upgradu** kontrolou stavu na webu Azure Portal:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   Prvním krokem procesu upgradu prochází operace škálování ("upgrade – Offline") ve všech relací bude ukončeno, a připojení se ukončí. 

   Druhým krokem procesu upgradu je migrace dat ("upgrade – Online"). Migrace dat je online skapat proces na pozadí. Tento proces pomalu přesune úložiště se sloupcovou strukturou data z původní architektura úložiště na nové architektuře úložiště použití místní mezipaměti SSD. Během této doby bude váš datový sklad pro dotazování a načítání online. Vaše data budou k dispozici pro dotazy bez ohledu na to, zda je před migrací nebo ne. Migrace dat se stane v závislosti na velikost vašich dat, úroveň výkonu a počet segmentů columnstore různou rychlostí. 

5. **Volitelné doporučení:** Po dokončení operace škálování můžete urychlit proces na pozadí data migrace. Přesun dat můžete vynutit spuštěním [Alter Index znovu sestavit](sql-data-warehouse-tables-index.md) ve všech tabulkách primární columnstore by dotazování na větší třídu cíle na úrovni služby a prostředky. Tato operace je **offline** ve srovnání s skapat proces na pozadí, což může trvat hodiny v závislosti na počtu a velikosti tabulek. Ale po dokončení migrace dat bude mnohem rychlejší, protože z důvodu novou architekturu rozšířené úložiště s vysoce kvalitní rowgroups.
 
> [!NOTE]
> Příkaz ALTER Index sestavení je v režimu offline operace a tabulky budou dostupné, až se dokončí sestavení.

Následující dotaz generuje požadované příkazy Alter Index Rebuild k urychlení migrace dat:

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
## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Upgrade z Azure zeměpisné oblasti pomocí obnovení na webu Azure portal

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Vytvoření bodu obnovení uživatelem definované pomocí webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte do SQL data warehouse, kterou chcete vytvořit bod obnovení pro.

3. V horní části přehledu, vyberte **+ nový bod obnovení**.

    ![Nový bod obnovení](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Zadejte název pro bod obnovení.

    ![Názvem bodu obnovení](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Obnovit databázi aktivní nebo pozastavena pomocí webu Azure portal
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte do SQL data warehouse, kterou chcete obnovit z.
3. V horní části přehledu, vyberte **obnovení**.

    ![ Obnovení – přehled](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Vyberte buď **body obnovení automaticky** nebo **uživatelem definované body obnovení**.

    ![Automatické body obnovení](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. Pro body obnovení uživatelsky definovaná **vyberte bod obnovení** nebo **vytvořit nový bod obnovení uživatelem definované**. Vyberte zeměpisnou oblast podporovaném serveru Gen2. 

    ![Uživatelem definované body obnovení](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Obnovení z Azure zeměpisné oblasti pomocí Powershellu
Chcete-li obnovit databázi, použijte [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) rutiny.

> [!NOTE]
> Můžete provést geografické obnovení na Gen2! Uděláte to tak, zadejte Gen2 ServiceObjectiveName (třeba DW1000**c**) jako volitelný parametr.
>

1. Otevřete Windows PowerShell.
2. Připojte se ke svému účtu Azure a vypsat všechna předplatná spojená s vaším účtem.
3. Vyberte předplatné, která obsahuje databázi obnovit.
4. Získáte databáze, kterou chcete obnovit.
5. Vytvořte žádost o obnovení pro databáze, určení Gen2 ServiceObjectiveName.
6. Ověřte stav databázi geograficky obnovit.

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Po dokončení obnovení konfigurace vaší databáze, najdete v článku [nakonfigurovat svou databázi po obnovení](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
>

Pokud zdrojová databáze je povolené šifrování TDE, bude obnovenou databázi povoleno TDE.


Pokud zaznamenáte problémy s váš datový sklad, vytvořte [žádost o podporu](sql-data-warehouse-get-started-create-support-ticket.md) a odkazovat na "Gen2 upgrade" jako možnou příčinu.

## <a name="next-steps"></a>Další postup
Váš upgradovaný datový sklad je online. Abyste mohli využívat rozšířenou architektury, najdete v článku [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).
