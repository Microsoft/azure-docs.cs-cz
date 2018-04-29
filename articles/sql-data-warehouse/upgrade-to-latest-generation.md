---
title: Upgrade na nejnovější generování Azure SQL Data Warehouse | Microsoft Docs
description: Upgrade na nejnovější generace architektury Azure hardware a úložiště Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 8ca8156d98932e0e7313375cadc1ace2a3088881
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optimalizace výkonu díky upgradu SQL Data Warehouse
Upgrade na nejnovější generace architektury Azure hardware a úložiště Azure SQL Data Warehouse.

## <a name="why-upgrade"></a>Proč upgradovat?
Teď můžete plynule upgradovat do SQL Data Warehouse Gen2 na portálu Azure. Pokud máte datový sklad Gen1, upgrade je vhodné. Upgradem, můžete použít nejnovější generování Azure hardwaru a rozšířená architektura úložiště. Můžete využít výhod vyšší výkon, vyšší škálovatelnost a neomezené sloupcovém úložiště. 

## <a name="applies-to"></a>Platí pro
Tento upgrade se vztahuje na Gen1 datových skladů.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Než začnete
> [!NOTE]
> Pokud vaše existující datový sklad Gen1 není v oblasti, kde je k dispozici Gen2, můžete [geografické obnovení do Gen2](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) pomocí prostředí PowerShell v podporované oblasti.
> 
>

1. Když je pozastavená Gen1 datového skladu k upgradu, [obnovit datového skladu](pause-and-resume-compute-portal.md).
2. Připravte se po několika minutách nečinnosti. 



## <a name="start-the-upgrade"></a>Spusťte upgrade

1. Přejděte na vaše Gen1 datový sklad v portálu Azure a klikněte na **upgradovat na Gen2**: ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. Ve výchozím nastavení **vyberte úroveň výkonu navrhované** pro datový sklad na základě vaší aktuální úroveň výkonu na Gen1 pomocí mapování níže:
    
| Gen1 | Gen2 |
| :----------------------: | :-------------------: |
|      OD DW100 – DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. Zajistěte, aby že vaše úloha byla dokončena spuštěná a uvedou před upgradem. Si všimnete výpadku pro několik minut, než je zpět do režimu online jako datový sklad Gen2 datového skladu. **Kliknutím na tlačítko upgradovat**. Cena úroveň výkonu Gen2 je aktuálně půl – vypnuté během období preview:
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitorování upgrade** kontrolou stavu na portálu Azure:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   Prvním krokem procesu upgradu projde operace škálování ("upgrade - Offline") budou ukončeny všechny relace, kde se zahodí připojení. 
   
   Druhým krokem procesu upgradu je migrace dat ("upgrade - Online"). Migrace dat je proces online skapat pozadí, které pomalu přesune sloupcová data z původní architektury úložiště do nové architektury úložiště využívat místní mezipaměti SSD. Během této doby bude online pro dotazování a načítání datového skladu. Všechna vaše data, bude k dispozici pro dotazy bez ohledu na to, jestli ho byla migrována nebo ne. Migrace dat se stane s různými rychlostí v závislosti na vaší velikost dat, vaše úroveň výkonu a počet segmentů columnstore. 

5. **Volitelné doporučení:** urychlit proces na pozadí migrace dat, doporučuje se okamžitě vynutit přesun dat spuštěním [opětovné sestavení Alter Index](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) ve všech tabulkách columnstore v větší SLO a prostředků Třída. Tato operace je offline ve srovnání s skapat proces na pozadí; migrace dat však budou být mnohem rychlejší kde lze poté využít všech výhod nové architektury rozšířené úložiště s vysokou kvalitu rowgroups po dokončení. 

Následující dotaz vygeneruje požadované příkazy Alter Index Rebuild urychlit proces migrace dat:

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



## <a name="next-steps"></a>Další postup
Upgradovaná datového skladu je online. Abyste mohli využívat vylepšené architektury, najdete v části [třídy prostředků pro úlohy správy](resource-classes-for-workload-management.md).
 
