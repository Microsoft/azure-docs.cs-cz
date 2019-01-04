---
title: Azure SQL Database souboru místo správy | Dokumentace Microsoftu
description: Tato stránka popisuje, jak spravovat místo souborů s využitím Azure SQL Database a obsahuje ukázky kódu pro zjištění, pokud je třeba zmenšit databázi také, jak k provádění zmenšit databázi operace.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: d8ddbb2590852ed80ce02f147886dc125815fc23
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605953"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Správa místo souborů ve službě Azure SQL Database
Tento článek popisuje různé druhy prostoru úložiště v Azure SQL Database a kroky, které mohou být provedeny, když přidělené místo souborů databáze a elastické fondy je potřeba explicitně spravovat.

## <a name="overview"></a>Přehled

Ve službě Azure SQL Database existují vzorce úlohy kde přidělení podkladové datové soubory pro databáze, mívá větší než velikost stránek používaná data. Tento stav může nastat v případě, že se zvýší množství využitého prostoru a data se následně odstraní. Důvodem je, protože přidělené místo souboru neuvolní automaticky, když se odstraní data.

V následujících scénářích může být potřeba monitorovat využití prostoru souborů a zmenšení datových souborů:
- Povolení růstu objemu dat v elastickém fondu v případě, že prostor souborů přidělený pro jeho databáze dosáhne maximální velikosti fondu
- Povolení snížení maximální velikosti jednoúčelové databáze nebo elastického fondu
- Povolení změny jednoúčelové databáze nebo elastického fondu na jinou úroveň služby nebo výkonu s nižší maximální velikostí

### <a name="monitoring-file-space-usage"></a>Monitorování využití místa na souboru
Většina metrik úložiště prostor zobrazí na webu Azure portal a rozhraní API pro následující míru jenom velikost stránky používaná data:
- Metriky rozhraní API využívající Azure Resource Manager včetně Powershellu [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

Ale následující rozhraní API také měření velikost místa vyhrazeného pro databáze a elastické fondy:
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Probíhá zmenšování souborů dat

Služba SQL DB automaticky nezmenší datové soubory uvolnění nevyužívaného místa přiděleného kvůli možnému dopadu na výkon databáze.  Zákazníci však může zmenšit datových souborů prostřednictvím samoobslužné v době podle vlastního uvážení pomocí následujících kroků popsaných v [Reclaim nevyužité přidělené místo na](#reclaim-unused-allocated-space). 

> [!NOTE]
> Na rozdíl od datových souborů služba SQL Database automaticky zmenší soubory protokolu od této operace nemá vliv na výkon databáze. 

## <a name="understanding-types-of-storage-space-for-a-database"></a>Principy typů prostor úložiště pro databázi

Principy následující množství prostoru úložiště jsou důležité pro správu místo souborů databáze.

|Množství databáze|Definice|Komentáře|
|---|---|---|
|**Využité dat**|Množství místa pro ukládání dat z databáze na stránkách velikosti 8 KB.|Obecně platí používá prostor zvyšuje (snižuje) na vložení (odstraní). Využité místo v některých případech se nezmění na vložení nebo odstraní podle toho, množství a vzor dat zahrnutých v operaci a jakékoli fragmentace. Například odstranění jednoho řádku z každé stránky dat nezmenší nutně využité místo.|
|**Přidělené místo na data**|Množství ve formátu souboru místo k dispozici pro ukládání dat z databáze.|Množství přidělené místo na automatické zvětšování, ale nikdy snižuje po odstranění. To zaručuje, že budoucí vkládání je rychlejší, protože prostor nemusí být přeformátovány.|
|**Data místo přidělené ale nepoužívá se**|Rozdíl mezi množství přidělené místo na data a data využité.|Toto množství představuje maximální množství volného místa, který se nedá uvolnit zmenšením datové soubory databáze.|
|**Maximální velikost dat**|Maximální množství místa, které lze použít pro ukládání dat z databáze.|Maximální velikost dat nelze nárůst množství přidělené místo na data.|
||||

Následující diagram znázorňuje vztah mezi různými typy prostor úložiště pro databázi.

![úložiště místo typů a vztahů](./media/sql-database-file-space-management/storage-types.png) 

## <a name="query-a-database-for-storage-space-information"></a>Dotaz na databázi pro informace o úložišti

Následující dotazy můžete použít k určení množství prostoru úložiště pro databázi.  

### <a name="database-data-space-used"></a>Použít místo data v databázi
Upravte následující dotaz, který vrací množství místa dat databáze použít.  Jednotky výsledku dotazu jsou v MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Přidělené místo na data databáze a nepoužívané přiděleného místa
Použijte tento dotaz vrátí velikost přidělené místo na data databáze a množství nevyužité místo přidělené.  Jednotky výsledku dotazu jsou v MB.

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
Upravte následující dotaz, který vrátí maximální velikost dat databáze.  Jednotky výsledku dotazu jsou v bajtech.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Principy typů prostor úložiště pro elastický fond

Principy následující množství prostoru úložiště jsou důležité pro správu místo souborů elastického fondu.

|Množství elastického fondu|Definice|Komentáře|
|---|---|---|
|**Využité dat**|Souhrn dat využité všechny databáze v elastickém fondu.||
|**Přidělené místo na data**|Souhrn dat přidělená všechny databáze v elastickém fondu.||
|**Data místo přidělené ale nepoužívá se**|Rozdíl mezi množství přidělené místo na data a data využité všechny databáze v elastickém fondu.|Toto množství představuje maximální velikost místa vyhrazeného pro elastický fond, který se nedá uvolnit zmenšením datové soubory databáze.|
|**Maximální velikost dat**|Maximální množství místa dat, který mohou využívat pro všechny jeho databáze elastického fondu.|Místo přidělené pro elastický fond by neměl být delší než maximální velikosti elastického fondu.  Pokud k tomuto stavu dochází, můžete místo přidělené, který se nepoužívá uvolní zmenšením datové soubory databáze.|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Dotaz elastického fondu pro informace o úložišti

Následující dotazy můžete použít k určení množství prostoru úložiště pro elastický fond.  

### <a name="elastic-pool-data-space-used"></a>Použít elastický fond datovému prostoru
Upravte následující dotaz, který vrací množství místa data elastický fond používá.  Jednotky výsledku dotazu jsou v MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Přidělené místo na data elastického fondu a nepoužívané přiděleného místa

Upravte následující skript Powershellu, který vrátí tabulku se seznamem místo přidělené a nevyužité místo přidělené pro každou databázi v elastickém fondu. V tabulce objednávky databází z těchto databází s co největší množství nevyužité přidělené místo na minimální množství nevyužité přidělené místo.  Jednotky výsledku dotazu jsou v MB.  

Výsledky dotazu k určení místa pro každou databázi ve fondu je možné přidat společně k určení celkové místo přidělené pro elastický fond. Přidělené místo elastický fond může být maximálně maximální velikosti elastického fondu.  

Skript prostředí PowerShell vyžaduje modul Powershellu pro službu SQL Server – viz [modul prostředí PowerShell stáhněte](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module?view=sql-server-2017) k instalaci.

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

### <a name="elastic-pool-data-max-size"></a>Maximální velikost dat elastického fondu

Upravte následující dotaz T-SQL, který vrací maximální velikost dat elastického fondu.  Jednotky výsledku dotazu jsou v MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Uvolnění nevyužívaného místa přiděleného

### <a name="dbcc-shrink"></a>Příkaz DBCC zmenšit

Po identifikaci databází pro opětovné získání nevyužité místo přidělené změnit název databáze v následujícím příkazu zmenšení souborů dat pro každou databázi.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Tento příkaz může ovlivnit výkon databáze, zatímco běží a pokud je to možné by se měl spustit během období s nízkým využitím.  

Další informace o tomto příkazu najdete v tématu [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 

### <a name="auto-shrink"></a>Auto-shrink

Automatické zmenšení Alternativně je možné povolit pro databázi.  Zmenšení automaticky sníží složitost správy souborů a je menší dopad na výkon databází než SHRINKDATABASE nebo SHRINKFILE.  Zmenšení automaticky může být zvláště užitečné pro Správa elastických fondů pomocí velkého počtu databází.  Automatické zmenšení však může být v uvolní místo souborů než SHRINKDATABASE a SHRINKFILE méně účinné.
Pokud chcete povolit automatické zmenšení, upravte název databáze v následujícím příkazu.


```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Další informace o tomto příkazu najdete v tématu [databázi nastavit](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=sql-server-2017) možnosti. 

### <a name="rebuild-indexes"></a>Provést nové sestavení indexů

Po datové soubory databáze jsou zmenšit, může fragmentovat. indexy a dojít ke ztrátě jejich účinnosti optimalizace výkonu. Pokud dojde k snížení výkonu, zvažte nové sestavení indexů databáze. Další informace o fragmentace a nové sestavení indexů, naleznete v tématu [Reorganize a znovu vytvořit indexy](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Další postup

- Informace o maximální velikosti databáze najdete tady:
  - [Založený na virtuálních jádrech zakoupení modelu omezení pro jednu databázi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [Omezení prostředků pro izolované databáze pomocí nákupní model založený na DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Založený na virtuálních jádrech zakoupení modelu limity pro elastické fondy Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [Omezení prostředků pro elastické fondy pomocí nákupní model založený na DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- Další informace o `SHRINKDATABASE` naleznete [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Další informace o fragmentace a nové sestavení indexů, naleznete v tématu [Reorganize a znovu vytvořit indexy](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
