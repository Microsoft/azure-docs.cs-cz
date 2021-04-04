---
title: Paralelní hromadné importy dat do tabulek oddílů SQL – proces týmu pro datové vědy
description: Vytvářejte dělené tabulky pro rychlé paralelní hromadné importy dat do databáze SQL Server.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 456e881d84697f4542f972ac0798cc95a3455b3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322408"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Sestavování a optimalizace tabulek pro rychlý paralelní import dat do SQL Server na virtuálním počítači Azure

Tento článek popisuje, jak vytvořit dělené tabulky pro rychlé paralelní hromadné importy dat do databáze SQL Server. Pro načítání a přenos velkých objemů dat do SQL Database je možné data importovat do databáze SQL a následné dotazy zlepšit pomocí *dělených tabulek a zobrazení*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Vytvoření nové databáze a sady skupin souborů
* [Vytvořte novou databázi](/sql/t-sql/statements/create-database-transact-sql), pokud již neexistuje.
* Přidejte do databáze skupiny souborů databáze, ve které jsou uloženy rozdělené fyzické soubory. 
* To lze provést pomocí příkazu [Create Database](/sql/t-sql/statements/create-database-transact-sql) , pokud databáze [](/sql/t-sql/statements/alter-database-transact-sql-set-options) již existuje.
* Přidejte jeden nebo více souborů (podle potřeby) do každé skupiny souborů databáze.
  
  > [!NOTE]
  > Zadejte cílovou skupina souborů, která obsahuje data pro tento oddíl a fyzické názvy souborů databáze, kde jsou uložena data skupiny souborů.
  > 
  > 

Následující příklad vytvoří novou databázi se třemi skupinami souborů kromě primární skupiny a skupin protokolů, které obsahují jeden fyzický soubor v každém z nich. Soubory databáze jsou vytvořeny ve výchozí složce SQL Server dat, jak je nakonfigurováno v instanci SQL Server. Další informace o výchozích umístěních souborů naleznete v tématu [umístění souborů pro výchozí a pojmenované instance SQL Server](/sql/sql-server/install/file-locations-for-default-and-named-instances-of-sql-server).

```sql
   DECLARE @data_path nvarchar(256);
   SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

   EXECUTE ('
      CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
```

## <a name="create-a-partitioned-table"></a>Vytvoření dělené tabulky
Chcete-li vytvořit dělené tabulky podle schématu dat, namapovaného na skupiny souborů databáze vytvořené v předchozím kroku, je nutné nejprve vytvořit funkci oddílu a schéma. Při hromadném importu dat do dělených tabulek se záznamy rozdělují mezi skupiny souborů podle schématu oddílu, jak je popsáno níže.

### <a name="1-create-a-partition-function"></a>1. vytvoření funkce oddílu
[Vytvoření funkce oddílu](/sql/t-sql/statements/create-partition-function-transact-sql) Tato funkce definuje rozsah hodnot nebo hranic, které mají být zahrnuty do jednotlivých tabulek oddílů, například pro omezení oddílů podle měsíce (některé \_ \_ pole DateTime) v roce 2013:
  
```sql
   CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
      AS RANGE RIGHT FOR VALUES (
         '20130201', '20130301', '20130401',
         '20130501', '20130601', '20130701', '20130801',
         '20130901', '20131001', '20131101', '20131201' )
```

### <a name="2-create-a-partition-scheme"></a>2. vytvoření schématu oddílu
[Vytvořte schéma oddílu](/sql/t-sql/statements/create-partition-scheme-transact-sql). Toto schéma mapuje každý rozsah oddílů ve funkci oddílu na fyzickou skupinu souborů, například:
  
```sql
      CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
```
 
Pokud chcete v každém oddílu ověřit rozsahy v závislosti na funkci nebo schématu, spusťte následující dotaz:
  
```sql
   SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
   FROM sys.partition_functions AS pfun
   INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
   INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
   WHERE pfun.name = <DatetimeFieldPFN>
```

### <a name="3-create-a-partition-table"></a>3. vytvoření tabulky oddílů
[Vytvořte dělené tabulky](/sql/t-sql/statements/create-table-transact-sql)podle schématu dat a určete pole schéma oddílu a omezení použité k dělení tabulky, například:
  
```sql
   CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)
```

Další informace najdete v tématu [vytváření dělených tabulek a indexů](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Hromadně importujte data pro každou jednotlivou tabulku oddílů.

* Můžete použít BCP, BULK INSERT nebo jiné metody, jako je například [Průvodce migrací SQL Server](https://sqlazuremw.codeplex.com/). Uvedený příklad používá metodu BCP.
* Změnou [databáze](/sql/t-sql/statements/alter-database-transact-sql-set-options) na změnu schématu protokolování transakcí BULK_LOGGEDte, aby se minimalizovala režie protokolování, například:
  
   ```sql
      ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
   ```
* Pro urychlení načítání dat spusťte paralelní operace hromadného importu. Tipy k urychlení hromadného importu velkých objemů dat do SQL Server databází najdete [v tématu Load 1 TB za méně než 1 hodinu](/archive/blogs/sqlcat/load-1tb-in-less-than-1-hour).

Následující skript prostředí PowerShell je příkladem paralelního načítání dat pomocí BCP.

```powershell
    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date
```

## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Vytváření indexů pro optimalizaci spojení a výkonu dotazů
* Pokud extrahujete data pro modelování z více tabulek, vytvořte indexy na klíčích JOIN pro zlepšení výkonu připojení.
* [Vytváření indexů](/sql/t-sql/statements/create-index-transact-sql) (clusterovaných nebo neclusterovaných) cílících na stejnou skupinu souborů pro každý oddíl, například:
  
```sql
   CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
--  or,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
 ```
 
  > [!NOTE]
  > Před hromadným importem dat se můžete rozhodnout vytvořit indexy. Vytvoření indexu před hromadným importem zpomaluje načítání dat.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Proces a technologie pro pokročilou analýzu v příkladu akce
Příklad uceleného podrobného průvodce pomocí vědeckého zpracování týmových dat s veřejnou datovou sadou najdete v tématu [vědecké zpracování týmových dat v akci: použití SQL Server](sql-walkthrough.md).