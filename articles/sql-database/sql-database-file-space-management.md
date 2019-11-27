---
title: Správa prostoru souborů pro jednu nebo více fondů databáze
description: Tato stránka popisuje, jak spravovat prostor souborů s jednou a sdruženými databázemi v Azure SQL Database a obsahuje ukázky kódu, jak určit, jestli potřebujete zmenšit jednu nebo ve fondu databáze, a jak provést operaci zmenšení databáze.
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
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420982"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Správa prostoru souborů pro databáze s jednou a fondem v Azure SQL Database

Tento článek popisuje různé typy prostoru úložiště pro databáze typu Single a pool v Azure SQL Database a kroky, které lze provést v případě, že je nutné explicitně spravovat prostor souborů přidělený pro databáze a elastické fondy.

> [!NOTE]
> Tento článek neplatí pro možnost nasazení spravované instance v Azure SQL Database.

## <a name="overview"></a>Přehled

U databází s jedním a sdruženým fondem v Azure SQL Database existují vzorce úloh, ve kterých může být přidělení základních datových souborů pro databáze větší než množství použitých datových stránek. Tento stav může nastat v případě, že se zvýší množství využitého prostoru a data se následně odstraní. Důvodem je skutečnost, že přidělené místo v souboru není automaticky uvolněno při odstranění dat.

V následujících scénářích může být potřeba monitorovat využití prostoru souborů a zmenšení datových souborů:

- Povolení růstu objemu dat v elastickém fondu v případě, že prostor souborů přidělený pro jeho databáze dosáhne maximální velikosti fondu
- Povolení snížení maximální velikosti jednoúčelové databáze nebo elastického fondu
- Povolení změny jednoúčelové databáze nebo elastického fondu na jinou úroveň služby nebo výkonu s nižší maximální velikostí

### <a name="monitoring-file-space-usage"></a>Monitorování využití místa na souboru

Většina metrik prostorů úložiště se zobrazuje v Azure Portal a následující rozhraní API měří pouze velikost použitých datových stránek:

- Rozhraní API metrik založené na Azure Resource Manager, včetně PowerShellu [Get – metrik](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric)
- T-SQL: [Sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

Následující rozhraní API však také měří velikost vyhrazeného místa pro databáze a elastické fondy:

- T-SQL: [Sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [Sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Zmenšení datových souborů

Služba SQL Database automaticky nezmenšuje datové soubory pro uvolnění nevyužitého přiděleného místa z důvodu potenciálního dopadu na výkon databáze.  Zákazníci ale můžou soubory dat zmenšit pomocí samoobslužné služby v době jejich výběru podle kroků popsaných v tématu [uvolnění nevyužitého vyhrazeného místa](#reclaim-unused-allocated-space).

> [!NOTE]
> Na rozdíl od datových souborů služba SQL Database automaticky zmenší soubory protokolu, protože tato operace nemá vliv na výkon databáze.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Porozumění typům úložného prostoru pro databázi

Při správě místa v databázi je důležité pochopit následující množství úložného prostoru.

|Množství databáze|Definice|Komentáře|
|---|---|---|
|**Využité místo pro data**|Velikost místa využitého k uložení dat databáze na stránkách 8 KB.|Obecně se využité místo zvětšuje (snižuje) při vložení (odstraní). V některých případech se využité místo nemění v závislosti na množství a vzoru dat zahrnutých v operaci a na jakékoli fragmentaci. Například odstranění jednoho řádku z každé datové stránky nemusí zbytečně snížit využité místo.|
|**Přidělené datové místo**|Velikost formátovaného místa v souboru, které je k dispozici pro ukládání dat databáze.|Velikost přiděleného místa se zvětšuje automaticky, ale po odstranění se nikdy nezkrátí. Toto chování zajistí rychlejší vkládání budoucích vložení, protože místo není nutné přeformátovat.|
|**Přidělené datové místo, ale nepoužívá se**|Rozdíl mezi objemem přiděleného datového prostoru a využitým datovým prostorem.|Toto množství představuje maximální množství volného místa, které může být uvolněno zmenšením datových souborů databáze.|
|**Maximální velikost dat**|Maximální množství místa, které lze použít pro ukládání databázových dat.|Velikost přiděleného datového prostoru nemůže být větší než maximální velikost dat.|

Následující diagram znázorňuje vztah mezi různými typy prostoru úložiště pro databázi.

![typy a vztahy prostoru úložiště](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Dotazování na izolovanou databázi pro informace o prostoru úložiště

K určení množství úložného prostoru pro izolovanou databázi lze použít následující dotazy.  

### <a name="database-data-space-used"></a>Využité místo pro data databáze

Upravte následující dotaz, který vrátí velikost využitého místa pro data databáze.  Jednotky výsledku dotazu jsou v MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Přidělené a nevyužité volné místo v databázi dat

Pomocí následujícího dotazu vraťte množství přiděleného místa pro data databáze a velikost přiděleného místa.  Jednotky výsledku dotazu jsou v MB.

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

Upravte následující dotaz tak, aby vracel maximální velikost dat databáze.  Počet jednotek výsledku dotazu je v bajtech.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Porozumění typům úložného prostoru pro elastický fond

Při správě prostoru pro elastický fond je důležité pochopit následující množství úložného prostoru.

|Množství elastického fondu|Definice|Komentáře|
|---|---|---|
|**Využité místo pro data**|Součet datových prostorů používaných všemi databázemi v elastickém fondu.||
|**Přidělené datové místo**|Součet datového prostoru přiděleného všemi databázemi v elastickém fondu.||
|**Přidělené datové místo, ale nepoužívá se**|Rozdíl mezi objemem přiděleného datového prostoru a datovým prostorem používaným všemi databázemi v elastickém fondu.|Toto množství představuje maximální prostor přidělený pro elastický fond, který je možné uvolnit zmenšením datových souborů databáze.|
|**Maximální velikost dat**|Maximální množství datového prostoru, které může elastický fond používat pro všechny jeho databáze.|Prostor přidělený elastickému fondu by neměl překročit maximální velikost elastického fondu.  Pokud k tomuto stavu dojde, může být přidělené místo, které není používáno, uvolněno zmenšením datových souborů databáze.|

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Dotazování elastického fondu pro informace o prostoru úložiště

K určení množství úložného prostoru pro elastický fond lze použít následující dotazy.  

### <a name="elastic-pool-data-space-used"></a>Využité místo datového prostoru elastického fondu

Upravte následující dotaz, který vrátí velikost využitého datového prostoru elastického fondu.  Jednotky výsledku dotazu jsou v MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Přidělené a nevyužité volné místo v datovém prostoru elastického fondu

Upravte následující příklady a vraťte tabulku se seznamem přiděleného místa a nevyužitého prostoru pro každou databázi v elastickém fondu. Tabulka seřadí databáze z těchto databází s největším množstvím nevyužitého vyhrazeného místa k minimálnímu množství nevyužitého přiděleného místa.  Jednotky výsledku dotazu jsou v MB.  

Výsledky dotazu pro určení prostoru přiděleného pro každou databázi ve fondu je možné přidat společně a určit tak celkové místo přidělené elastickému fondu. Přidělené místo elastického fondu by nemělo přesáhnout maximální velikost elastického fondu.  

> [!IMPORTANT]
> Modul Azure Resource Manager PowerShellu (RM) je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou k dispozici pro modul AZ. SQL. V modulu AzureRM bude i nadále docházet k opravám chyb až do prosince 2020.  Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Další informace o kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

Skript prostředí PowerShell vyžaduje SQL Server modul prostředí PowerShell – viz téma [stažení modulu PowerShell](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) k instalaci.

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

Následující snímek obrazovky ukazuje příklad výstupu skriptu:

![Příklad přiděleného prostoru elastického fondu a nevyužitého místa přidělení](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Maximální velikost dat elastického fondu

Upravte následující dotaz T-SQL, který vrátí maximální velikost dat elastického fondu.  Jednotky výsledku dotazu jsou v MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Uvolnění nevyužitého přiděleného místa

> [!NOTE]
> Tento příkaz může mít vliv na výkon databáze, pokud je spuštěný, a pokud je to možné, měly by být spuštěny během období nízkého využití.

### <a name="dbcc-shrink"></a>Sbalení příkazu DBCC

Jakmile zjistíte, že databáze byly zjištěny pro získání nevyužitého přiděleného místa, upravte název databáze v následujícím příkazu, aby se zmenšily datové soubory pro každou databázi.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Tento příkaz může mít vliv na výkon databáze, pokud je spuštěný, a pokud je to možné, měly by být spuštěny během období nízkého využití.  

Další informace o tomto příkazu najdete v tématu [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

### <a name="auto-shrink"></a>Automatické zmenšení

Alternativně lze pro databázi povolit automatické zmenšení.  Automatické zmenšení snižuje složitost správy souborů a je méně ovlivněná na výkon databáze, než `SHRINKDATABASE` nebo `SHRINKFILE`.  Automatické zmenšení může být užitečné hlavně při správě elastických fondů s mnoha databázemi.  Automatické zmenšení ale může být méně účinné při uvolnění místa v souboru, než `SHRINKDATABASE` a `SHRINKFILE`.
Chcete-li povolit automatické zmenšení, upravte název databáze v následujícím příkazu.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Další informace o tomto příkazu najdete v tématu možnosti [sady databáze](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) .

### <a name="rebuild-indexes"></a>Opětovné sestavení indexů

Až budou soubory dat databáze zmenšené, indexy se můžou fragmentovat a ztratit jejich efektivitu optimalizace výkonu. Pokud dojde ke snížení výkonu, zvažte opakované sestavení indexů databáze. Další informace o fragmentaci a opětovném sestavování indexů najdete v tématu [reorganizace a opětovné sestavení indexů](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Další kroky

- Informace o maximální velikosti databáze najdete v těchto tématech:
  - [Azure SQL Database omezení pro nákupní model založený na vCore pro jednu databázi](sql-database-vcore-resource-limits-single-databases.md)
  - [Omezení prostředků pro izolované databáze s využitím nákupního modelu založeného na DTU](sql-database-dtu-resource-limits-single-databases.md)
  - [Azure SQL Database omezení pro nákupní model založený na vCore pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md)
  - [Omezení prostředků pro elastické fondy pomocí nákupního modelu založeného na DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Další informace o příkazu `SHRINKDATABASE` naleznete v tématu [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Další informace o fragmentaci a opětovném sestavování indexů najdete v tématu [reorganizace a opětovné sestavení indexů](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
