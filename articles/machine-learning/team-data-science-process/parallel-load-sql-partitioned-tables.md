---
title: Paralelní hromadný import dat v tabulkách oddílů SQL – proces vědecké analýzy týmových dat
description: Vytvořte rozdělené tabulky pro rychlý paralelní hromadný import dat do databáze serveru SQL Server.
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
ms.openlocfilehash: 673a801e218d055bf482dc97972e36584cddd402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721332"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Vytváření a optimalizace tabulek pro rychlý paralelní import dat do SQL Serveru na virtuálním počítači Azure

Tento článek popisuje, jak vytvořit rozdělené tabulky pro rychlý paralelní hromadný import dat do databáze serveru SQL Server. Pro načítání a přenos velkých objemů dat do databáze SQL lze import dat do databáze SQL DB a následných dotazů zlepšit pomocí *dělených tabulek a zobrazení*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Vytvoření nové databáze a sady skupin souborů
* [Vytvořte novou databázi](https://technet.microsoft.com/library/ms176061.aspx), pokud již neexistuje.
* Přidejte do databáze soubory databáze, které uchovává fyzické soubory s oddíly. 
* To lze provést pomocí [create database,](https://technet.microsoft.com/library/ms176061.aspx) pokud nové nebo [ALTER databáze,](https://msdn.microsoft.com/library/bb522682.aspx) pokud databáze již existuje.
* Přidejte jeden nebo více souborů (podle potřeby) do každé skupiny souborů databáze.
  
  > [!NOTE]
  > Zadejte cílovou skupinu souborů, která obsahuje data pro tento oddíl a názvy souborů fyzické databáze, ve kterých jsou uložena data skupiny souborů.
  > 
  > 

Následující příklad vytvoří novou databázi se třemi skupinami souborů než primární a skupiny protokolů, obsahující jeden fyzický soubor v každém. Databázové soubory jsou vytvořeny ve výchozí složce Dat serveru SQL Server, jak je nakonfigurováno v instanci serveru SQL Server. Další informace o výchozích umístěních souborů naleznete [v tématu Umístění souborů pro výchozí a pojmenované instance serveru SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

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

## <a name="create-a-partitioned-table"></a>Vytvoření rozdělené tabulky
Chcete-li vytvořit rozdělené tabulky podle schématu dat mapované na soubory souborů databáze vytvořené v předchozím kroku, musíte nejprve vytvořit funkci oddílu a schéma. Pokud jsou data hromadně importována do dělených tabulek, záznamy jsou rozděleny mezi skupiny souborů podle schématu oddílů, jak je popsáno níže.

### <a name="1-create-a-partition-function"></a>1. Vytvoření funkce oddílu
[Vytvoření funkce oddílu](https://msdn.microsoft.com/library/ms187802.aspx) Tato funkce definuje rozsah hodnot/hranic, které mají být zahrnuty do každé jednotlivé tabulky\_oddílů, například pro omezení oddílů podle měsíce (některé pole datatime)\_v roce 2013:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

### <a name="2-create-a-partition-scheme"></a>2. Vytvoření schématu oddílů
[Vytvořte schéma oddílů](https://msdn.microsoft.com/library/ms179854.aspx). Toto schéma mapuje každý rozsah oddílů ve funkci oddílu na fyzickou skupinu souborů, například:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Chcete-li ověřit rozsahy platné v každém oddílu podle funkce/schématu, spusťte následující dotaz:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

### <a name="3-create-a-partition-table"></a>3. Vytvoření tabulky oddílů
[Vytvořte rozdělenou tabulku](https://msdn.microsoft.com/library/ms174979.aspx)(tabulky) podle schématu dat a zadejte schéma oddílů a pole omezení použité k rozdělení tabulky, například:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Další informace naleznete v [tématu Vytváření dělených tabulek a indexů](https://msdn.microsoft.com/library/ms188730.aspx).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Hromadné importdat pro každou jednotlivou tabulku oddílů

* Můžete použít BCP, BULK INSERT nebo jiné metody, jako je [například Průvodce migrací serveru SQL Server](https://sqlazuremw.codeplex.com/). Uvedený příklad používá metodu BCP.
* [Změňte databázi](https://msdn.microsoft.com/library/bb522682.aspx) a změňte schéma protokolování transakcí tak, aby BULK_LOGGED minimalizovat režii protokolování, například:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Chcete-li urychlit načítání dat, spusťte operace hromadného importu paralelně. Tipy pro urychlení hromadného importu velkých objemů dat do databází serveru SQL Server naleznete [v tématu Load 1 TB za méně než 1 hodinu](https://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

Následující skript prostředí PowerShell je příkladem paralelního načítání dat pomocí BCP.

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


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Vytvoření indexů pro optimalizaci spojení a výkonu dotazů
* Pokud extrahujete data pro modelování z více tabulek, vytvořte indexy na klíče spojení ke zlepšení výkonu spojení.
* [Vytvořte indexy](https://technet.microsoft.com/library/ms188783.aspx) (clusterované nebo neclusterované) zaměřené na stejnou skupinu souborů pro každý oddíl, například:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  Nebo
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Můžete se rozhodnout vytvořit indexy před hromadným importem dat. Vytváření indexu před hromadným importem zpomaluje načítání dat.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Příklad pokročilého analytického procesu a technologie v akci
Příklad úvodního návodu pomocí procesu vědecké analýzy týmových dat s veřejnou datovou sadou naleznete [v tématu Proces vědecké ho procesu týmových dat v akci: pomocí serveru SQL Server](sql-walkthrough.md).

