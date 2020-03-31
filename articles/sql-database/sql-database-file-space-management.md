---
title: Správa prostoru souborů s jednou/sdruženou databází
description: Tato stránka popisuje, jak spravovat místo v souborech s jednu a sdružené databáze v Azure SQL Database a poskytuje ukázky kódu, jak zjistit, zda je potřeba zmenšit jednu nebo sdružené databáze, stejně jako jak provést databázi zmenšit operace.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 007bbffbd7c4fcad339f88eb78991eb39fb829e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420982"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Správa místa v souborech pro jednu a sdružené databáze v Azure SQL Database

Tento článek popisuje různé typy úložného prostoru pro jednu a sdružené databáze v Azure SQL Database a kroky, které lze přijmout, když je třeba explicitně spravovat místo v souborech přidělené pro databáze a elastické fondy.

> [!NOTE]
> Tento článek se nevztahuje na možnost nasazení spravované instance v Azure SQL Database.

## <a name="overview"></a>Přehled

S jednu a sdružené databáze v Azure SQL Database existují vzory úloh, kde přidělení podkladových datových souborů pro databáze může být větší než množství použitých datových stránek. Tento stav může nastat v případě, že se zvýší množství využitého prostoru a data se následně odstraní. Důvodem je, že přidělené místo v souboru není automaticky regenerované při odstranění dat.

V následujících scénářích může být potřeba monitorovat využití prostoru souborů a zmenšení datových souborů:

- Povolení růstu objemu dat v elastickém fondu v případě, že prostor souborů přidělený pro jeho databáze dosáhne maximální velikosti fondu
- Povolení snížení maximální velikosti jednoúčelové databáze nebo elastického fondu
- Povolení změny jednoúčelové databáze nebo elastického fondu na jinou úroveň služby nebo výkonu s nižší maximální velikostí

### <a name="monitoring-file-space-usage"></a>Sledování využití místa souboru

Většina metrik úložného prostoru zobrazených na webu Azure Portal a následující api měří jenom velikost použitých datových stránek:

- Azure Resource Manager na metriky api, včetně [metriky get-](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

Následující api však také měří velikost místa přiděleného pro databáze a elastické fondy:

- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Zmenšení datových souborů

Služba SQL Database automaticky nezmenšuje datové soubory, aby získala nevyužité přidělené místo z důvodu potenciálního dopadu na výkon databáze.  Zákazníci však mohou zmenšit datové soubory prostřednictvím samoobslužné služby v době, kdy se jim to rozhodne, a to podle kroků popsaných v [rekultivaci nevyužitého přiděleného místa](#reclaim-unused-allocated-space).

> [!NOTE]
> Na rozdíl od datových souborů služba SQL Database automaticky zmenší soubory protokolu, protože tato operace nemá vliv na výkon databáze.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Principy typů úložného prostoru pro databázi

Pochopení následujícímnožství úložného prostoru jsou důležité pro správu prostoru souboru databáze.

|Množství databáze|Definice|Komentáře|
|---|---|---|
|**Využitý datový prostor**|Velikost místa použitého k ukládání dat databáze na 8 stránkách KB.|Obecně platí, že využité místo se zvyšuje (zmenšuje) na vložky (odstraní). V některých případech se využité místo nemění na vloží nebo odstraní v závislosti na množství a vzor dat zapojených do operace a jakékoli fragmentace. Například odstranění jednoho řádku z každé datové stránky nemusí nutně snížit využité místo.|
|**Přidělený datový prostor**|Velikost formátovaného místa souboru, které je k dispozici pro ukládání dat databáze.|Množství přiděleného místa se automaticky zvětšuje, ale po odstranění se nikdy nesníží. Toto chování zajišťuje, že budoucí vloží jsou rychlejší, protože prostor není nutné přeformátovat.|
|**Přidělené, ale nevyužité místo dat**|Rozdíl mezi množstvím přiděleného datového prostoru a využitého datového prostoru.|Toto množství představuje maximální množství volného místa, které lze uvolnit zmenšením datových souborů databáze.|
|**Maximální velikost dat**|Maximální množství místa, které lze použít pro ukládání dat databáze.|Velikost přiděleného datového prostoru nemůže přesáhnout maximální velikost dat.|

Následující diagram znázorňuje vztah mezi různými typy úložného prostoru pro databázi.

![typy a vztahy úložného prostoru](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Dotaz na jednu databázi pro informace o úložném prostoru

Následující dotazy lze určit množství úložného prostoru pro jednu databázi.  

### <a name="database-data-space-used"></a>Využité datové místo databáze

Upravte následující dotaz a vraťte velikost využitého datového prostoru databáze.  Jednotky výsledku dotazu jsou v MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Přidělené a nevyužité přidělené místo v databázi dat

Následující dotaz slouží k vrácení množství přiděleného datového prostoru databáze a množství přiděleného nevyužitého místa.  Jednotky výsledku dotazu jsou v MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```

### <a name="database-data-max-size"></a>Maximální velikost dat databáze

Upravte následující dotaz a vraťte maximální velikost dat databáze.  Jednotky výsledku dotazu jsou v bajtech.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Principy typů úložného prostoru pro elastický fond

Pochopení následující množství úložného prostoru jsou důležité pro správu prostoru souboru elastického fondu.

|Množství elastického fondu|Definice|Komentáře|
|---|---|---|
|**Využitý datový prostor**|Součet datového prostoru používaného všemi databázemi v elastickém fondu.||
|**Přidělený datový prostor**|Součet datového prostoru přidělené všechny databáze v elastickém fondu.||
|**Přidělené, ale nevyužité místo dat**|Rozdíl mezi množstvím přiděleného datového prostoru a datovým prostorem používaným všemi databázemi v elastickém fondu.|Toto množství představuje maximální množství místa přidělené pro elastický fond, který lze uvolnit zmenšením datových souborů databáze.|
|**Maximální velikost dat**|Maximální množství datového prostoru, který může elastický fond použít pro všechny jeho databáze.|Místo přidělené pro elastický fond by nemělo překročit maximální velikost elastického fondu.  Pokud dojde k této podmínce, pak místo přidělené, které je nevyužité lze uvolnit zmenšením datových souborů databáze.|

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Dotaz na elastický fond pro informace o prostoru úložiště

Následující dotazy lze určit množství prostoru úložiště pro elastický fond.  

### <a name="elastic-pool-data-space-used"></a>Využité datové místo elastického fondu

Upravte následující dotaz a vraťte velikost použitého datového prostoru elastického fondu.  Jednotky výsledku dotazu jsou v MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Přidělené a nevyužité přidělené místo datového fondu elastického fondu

Upravte následující příklady a vraťte tabulku se seznamem přiděleného a nevyužitého místa pro každou databázi v elastickém fondu. Tabulka objednává databáze z těchto databází s největším množstvím nevyužitého přiděleného místa na nejmenší množství nevyužitého přiděleného místa.  Jednotky výsledku dotazu jsou v MB.  

Výsledky dotazu pro určení místa přidělené pro každou databázi ve fondu lze sečíst k určení celkového místa přidělené pro elastický fond. Přidělené místo elastického fondu by nemělo překročit maximální velikost elastického fondu.  

> [!IMPORTANT]
> Modul Správce prostředků Azure (RM) prostředí PowerShell je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Modul AzureRM bude nadále dostávat opravy chyb nejméně do prosince 2020.  Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické. Další informace o jejich kompatibilitě [najdete v tématu Představení nového modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Skript PowerShellu vyžaduje modul SQL Server PowerShell – viz [Stažení modulu PowerShellu](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) k instalaci.

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

Následující snímek obrazovky je příkladem výstupu skriptu:

![elastický fond přidělené místo a nevyužité přidělené místo příklad](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Maximální velikost dat elastického fondu

Upravte následující dotaz T-SQL a vraťte maximální velikost dat elastického fondu.  Jednotky výsledku dotazu jsou v MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Uvolnit nevyužité přidělené místo

> [!NOTE]
> Tento příkaz může ovlivnit výkon databáze, když je spuštěn, a pokud je to možné, by měl být spuštěn během období nízké využití.

### <a name="dbcc-shrink"></a>Zmenšit DBCC

Jakmile jsou databáze identifikovány pro rekultivaci nevyužitého přiděleného místa, upravte název databáze v následujícím příkazu a zmenšete datové soubory pro každou databázi.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Tento příkaz může ovlivnit výkon databáze, když je spuštěn, a pokud je to možné, by měl být spuštěn během období nízké využití.  

Další informace o tomto příkazu naleznete v tématu [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

### <a name="auto-shrink"></a>Automatické zmenšení

Alternativně auto zmenšit lze povolit pro databázi.  Automatické zmenšení snižuje složitost správy souborů a má `SHRINKDATABASE` `SHRINKFILE`menší dopad na výkon databáze než nebo .  Automatické zmenšení může být užitečné zejména pro správu elastických fondů s mnoha databázemi.  Automatické zmenšení však může být méně efektivní `SHRINKDATABASE` `SHRINKFILE`při rekultivaci místa souboru než a .
Chcete-li povolit automatické zmenšení, upravte název databáze v následujícím příkazu.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Další informace o tomto příkazu naleznete v [tématu DATABASE SET](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) options.

### <a name="rebuild-indexes"></a>Znovu sestavit indexy

Po zmenšení datových souborů databáze indexy může dojít k fragmentaci a ztratí účinnost optimalizace výkonu. Pokud dojde ke snížení výkonu, zvažte opětovné sestavení indexů databáze. Další informace o fragmentaci a opětovné sestavení indexy, naleznete v [tématu Reorganizovat a znovu sestavit indexy](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Další kroky

- Informace o maximálnívelikosti databáze naleznete v tématu:
  - [Limity nákupního modelu na základě virtuálních jader Azure SQL Database pro jednu databázi](sql-database-vcore-resource-limits-single-databases.md)
  - [Omezení prostředků pro jednotlivé databáze používající nákupní model založený na DTU](sql-database-dtu-resource-limits-single-databases.md)
  - [Limity nákupních modelů založených na virtuálních jádrech Azure SQL Database pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md)
  - [Omezení prostředků pro elastické fondy pomocí nákupního modelu založeného na DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Další informace o `SHRINKDATABASE` příkazu naleznete v tématu [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Další informace o fragmentaci a opětovné sestavení indexy, naleznete v [tématu Reorganizovat a znovu sestavit indexy](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
