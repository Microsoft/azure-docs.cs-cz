---
title: Azure SQL Database souboru místo správy | Dokumentace Microsoftu
description: Tato stránka popisuje, jak spravovat místo souborů s využitím Azure SQL Database a obsahuje ukázky kódu pro zjištění, pokud je třeba zmenšit databázi také, jak k provádění zmenšit databázi operace.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 1ecc0ce08ef42f5f5935bca29e8269be2ea142f0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415145"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Správa místo souborů ve službě Azure SQL Database

Tento článek popisuje různé druhy prostoru úložiště v Azure SQL Database a kroky, které mohou být provedeny, když přidělené místo souborů databáze a elastické fondy je potřeba spravovat zákazník.

## <a name="overview"></a>Přehled

Ve službě Azure SQL Database měření metriky velikost úložiště zobrazí v portálu Azure portal a rozhraní API pro následující počet stránek používaná data pro databáze a elastické fondy:
- Metriky rozhraní API využívající Azure Resource Manager včetně Powershellu [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

Existují vzorce úlohy, ve kterých bude přidělení prostoru v podkladové datové soubory pro databáze větší než počet stránek používaných dat v datových souborů. Tato situace může nastat, když používá zvyšuje prostor a pak data se pak odstraní. Při odstranění dat přidělené místo souboru není uvolní automaticky, když se odstraní data. V takových scénářích do přiděleného místa pro databáze nebo fondu mohou překročit stanovený maximální podporovaná omezení systému (nebo nepodporuje) pro databázi a v důsledku toho zabránit nárůstu objemu dat nebo zabránit změnám úroveň výkonu, i když ve skutečnosti využité místo databáze je menší než je maximální počet omezení prostoru. Pokud chcete zmírnit, budete muset zmenšení databáze ke snížení přidělené, ale nevyužité místo v databázi.

Služba SQL Database není automaticky zmenšit databázové soubory uvolnění nevyužívaného místa přiděleného kvůli potenciální dopad na výkon databáze. Však můžete zmenšit souboru v databázi v době podle vašeho výběru pomocí následujících kroků popsaných v [Reclaim nevyužité přidělené místo na](#reclaim-unused-allocated-space). 

> [!NOTE]
> Na rozdíl od datových souborů služba SQL Database automaticky zmenší soubory protokolu od této operace nemá vliv na výkon databáze.

## <a name="understanding-the-types-of-storage-space-for-a-database"></a>Principy typů prostor úložiště pro databázi

Ke správě adresního prostoru soubor, budete muset porozumět následující termíny týkající se úložiště databáze pro oba izolované databáze a elastického fondu.

|Termín prostor úložiště|Definice|Komentáře|
|---|---|---|
|**Využité dat**|Množství místa pro ukládání dat z databáze na stránkách velikosti 8 KB.|Obecně platí používá tento prostor zvyšuje (snižuje) na vložení (odstraní). Využité místo v některých případech se nezmění na vložení nebo odstraní podle toho, množství a vzor dat zahrnutých v operaci a jakékoli fragmentace. Například odstranění jednoho řádku z každé stránky dat nezmenší nutně využité místo.|
|**Přidělené místo**|Množství ve formátu souboru místo k dispozici pro ukládání dat z databáze|Místo přidělené automaticky roste, ale nikdy snižuje po odstranění. To zaručuje, že budoucí vkládání je rychlejší, protože prostor nemusí být přeformátovány.|
|**Místo přidělené ale nepoužívá se**|Množství nepoužívaná data souboru místo přidělené pro databázi.|Toto množství je rozdíl mezi množství přidělené místo a využité a představuje maximální množství místa, které se nedá uvolnit zmenšením souborů databáze.|
|**Maximální velikost**|Maximální množství dat místa, které je možné v databázi.|Mimo maximální velikost dat nelze rozšiřovat přidělené místo data.|
||||

Následující diagram znázorňuje vztah mezi typy úložného prostoru.

![úložiště místo typů a vztahů](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-database-for-storage-space-information"></a>Dotaz na databázi pro informace o úložišti

Chcete-li zjistit, pokud jste přidělili ale nepoužívá se datovému prostoru pro jednotlivé databáze, možná budete chtít získat zpět, použijte následující dotazy:

### <a name="database-data-space-used"></a>Použít místo data v databázi
Upravte následující dotaz, který vrátí velikost databáze data využité místo v MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-allocated-and-allocated-space-unused"></a>Přidělené a přidělené místo na nepoužívaných dat z databáze
Následující dotaz vrátí množství dat z databáze přidělené a přidělené místo na nevyužité změňte.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-max-size"></a>Maximální velikost databáze
Upravte následující dotaz, který vrátí maximální velikost databáze v bajtech.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Dotaz elastického fondu pro informace o úložišti

Chcete-li zjistit, pokud jste přidělili ale nepoužívá se data místa v elastickém fondu a pro každou databázi ve fondu, možná budete chtít získat zpět, použijte následující dotazy:

### <a name="elastic-pool-data-space-used"></a>Použít elastický fond datovému prostoru
Upravte následující dotaz, který vrací množství elastického fondu dat využité místo v MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-allocated-and-allocated-space-unused"></a>Přidělené a přidělené místo na nevyužité dat elastického fondu

Upravte následující skript Powershellu pro vrátí tabulku se seznamem celkové přidělené místo a nevyužité místo přidělené pro každou databázi v elastickém fondu. V tabulce objednávky databáze od těch, které s největší množství přidělené místo na nevyužité minimální množství přidělené místo na nevyužité.  

Výsledky dotazu k určení místa pro každou databázi ve fondu je možné přidat společně k určení přiděleno místo elastického fondu. Přidělené místo elastický fond může být maximálně maximální velikosti elastického fondu.  

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzureRmSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
# Requires SQL Server PowerShell module – see here to install.  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

Na následujícím snímku obrazovky je příklad výstupu skriptu:

![elastický fond přidělené příklad nevyužité přiděleného místa a místa](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-max-size"></a>Maximální velikost elastického fondu

Použijte tento dotaz T-SQL k vrácení elastické databáze maximální velikost v MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Uvolnění nevyužívaného místa přiděleného

Jakmile určíte, že máte nevyužité přidělené místo, pro kterou chcete získat zpět, použijte následující příkaz pro zmenšení přidělené místo databáze. 

> [!IMPORTANT]
> Pro databáze v elastickém fondu databází s nejvíce místo přidělené nevyužité by měl zmenšit, nejdřív uvolnit místo souboru nejrychleji.  

Která zmenší všechny datové soubory v zadané databázi, použijte následující příkaz:

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'<database_name>')
```

Další informace o tomto příkazu najdete v tématu [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

> [!IMPORTANT] 
> Vezměte v úvahu, jsou zmenšit omnovení indexy databáze po datové soubory databáze, může fragmentovat. indexy a přijít o jejich účinnosti optimalizace výkonu. Pokud k tomu dojde, poté indexy, které by měl znovu sestavit. Další informace o fragmentace a nové sestavení indexů, naleznete v tématu [Reorganize a znovu vytvořit indexy](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Další postup

- Informace o maximální velikosti najdete tady:
  - [Založený na virtuálních jádrech zakoupení modelu omezení pro jednu databázi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [Omezení prostředků pro izolované databáze pomocí nákupní model založený na DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Založený na virtuálních jádrech zakoupení modelu limity pro elastické fondy Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [Omezení prostředků pro elastické fondy pomocí nákupní model založený na DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- Další informace o `SHRINKDATABASE` naleznete [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Další informace o fragmentace a nové sestavení indexů, naleznete v tématu [Reorganize a znovu vytvořit indexy](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).