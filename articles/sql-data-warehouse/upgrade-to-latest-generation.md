---
title: Upgrade na nejnovější generace služby Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Upgradujte na nejnovější generace architektury Azure hardware a úložiště Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/22/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: fe1f2e026aaa4260d34b9b1cb96064053af1c3c7
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568008"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optimalizace výkonu díky upgradu SQL Data Warehouse
Upgradujte na nejnovější generace architektury Azure hardware a úložiště Azure SQL Data Warehouse.

## <a name="why-upgrade"></a>Proč upgradovat?
Nově můžete bez problémů upgradovat na úroveň SQL Data Warehouse – Compute optimalizované Gen2 na webu Azure Portal. Pokud máte datový sklad – Compute optimalizované Gen1 vrstvy, upgrade se doporučuje. Díky upgradu, můžete použít nejnovější generace hardwaru Azure a rozšířená architektura úložiště. Můžete využít výhod vyšší výkon, vyšší škálovatelnost a neomezené sloupcové úložiště. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Platí pro
Tento upgrade se vztahuje na – Compute optimalizované Gen1 úroveň datových skladů.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Než začnete
> [!NOTE]
> Pokud vaše existující datový sklad – Compute optimalizované Gen1 vrstvy není v oblasti, kde je k dispozici na úrovni Gen2 – Compute optimalizované, můžete si [geografické obnovení](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) prostřednictvím prostředí PowerShell v podporované oblasti.
> 
>

1. Pokud datový sklad vrstvy – Compute optimalizované Gen1 k upgradu je pozastavený, [obnovit datový sklad](pause-and-resume-compute-portal.md).
2. Připravit na pár minut prostojů. 



## <a name="start-the-upgrade"></a>Spuštění upgradu

1. Přejděte na vaše – Compute optimalizované Gen1 úroveň datového skladu na webu Azure Portal a klikněte na **upgradovat na Gen2** karty na kartě úlohy: ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
> [!NOTE]
> Pokud se nezobrazí **upgradovat na Gen2** karet na kartě úlohy, typ vašeho předplatného je omezený v aktuální oblasti. [Vyplňte lístek podpory](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) získat vaše předplatné povolený.

2. Ve výchozím nastavení **vyberte úroveň výkonu navrhované** pro datový sklad na základě vaší aktuální úroveň výkonu na úrovni Gen1 – Compute optimalizované pomocí následující mapování:
    
   | Služba COMPUTE úrovně optimalizované Gen1 | Služba COMPUTE úrovně optimalizované Gen2 |
   | :----------------------: | :-------------------: |
   |      DW100 – ÚROVEŇ DW600       |        DW500c         |
   |          DW1000          |        DW1000c        |
   |          DW1200          |        DW1500c        |
   |          DW1500          |        DW1500c        |
   |          DW2000          |        DW2000c        |
   |          DW3000          |        DW3000c        |
   |          DW6000          |        DW6000c        |

3. Zajistěte, aby že úloha byla dokončena spuštění a nečinná před upgradem. Výpadek nastane pro několik minut, než data warehouse je zpátky do online režimu jako datový sklad – Compute optimalizované Gen2 vrstvy. **Kliknutím na tlačítko upgradovat**. Do ceny dané úrovně výpočetních optimalizované Gen2 úroveň výkonu je aktuálně polovině odsouhlasení během období preview:
    
   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitorování upgradu** kontrolou stavu na webu Azure Portal:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   Prvním krokem procesu upgradu prochází operace škálování ("upgrade – Offline") ve všech relací bude ukončeno, a připojení se ukončí. 
   
   Druhým krokem procesu upgradu je migrace dat ("upgrade – Online"). Migrace dat je proces skapat online na pozadí, který pomalu pohybuje sloupcová data ze starého architektura úložiště na nové architektuře úložiště využití místní mezipaměti SSD. Během této doby bude váš datový sklad pro dotazování a načítání online. Všechna vaše data budou k dispozici pro dotazy bez ohledu na to, zda je před migrací nebo ne. Migrace dat se stane s různou rychlostí v závislosti na velikost vašich dat, úroveň výkonu a počet segmentů columnstore. 

5. **Volitelné doporučení:** urychlit proces na pozadí migrace dat, můžete okamžitě vynutit přesun dat spuštěním [Alter Index znovu sestavit](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) ve všech tabulkách primární columnstore je by dotazování na vyšší SLO a třída prostředků. Tato operace je **offline** ve srovnání s procesy na pozadí skapat, což může trvat hodiny v závislosti na počtu a velikosti tabulek, ale migrace dat bude mnohem rychlejší, protože ve kterém mohou následně využít výhod nové architektury rozšířené úložiště po dokončení s vysoce kvalitní rowgroups. 

Následující dotaz generuje požadované příkazy Alter Index Rebuild urychlit proces migrace dat:

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
Váš upgradovaný datový sklad je online. Abyste mohli využívat rozšířenou architektury, najdete v článku [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).
 
