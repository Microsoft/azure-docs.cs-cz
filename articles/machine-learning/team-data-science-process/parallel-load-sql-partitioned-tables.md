---
title: Vytváření a optimalizaci tabulky pro rychlé paralelní import dat do systému SQL Server na Virtuálním počítači Azure | Dokumentace Microsoftu
description: Paralelní hromadný import dat pomocí tabulek oddílů SQL
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ff90fdb0-5bc7-49e8-aee7-678b54f901c8
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: c318411fe17fb60c1c0bf991a07b46a515252952
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683207"
---
# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>Paralelní hromadný import dat pomocí tabulek oddílů SQL

Tento článek popisuje, jak vytvářet dělené tabulky pro rychlé paralelní hromadný import dat do databáze SQL serveru. Pro načítání velkých objemů dat nebo přenos do služby SQL database, import dat do SQL DB a dalších dotazů lze zvýšit pomocí *dělené tabulky a zobrazení*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Vytvořit novou databázi a sadu skupin souborů
* [Vytvořit novou databázi](https://technet.microsoft.com/library/ms176061.aspx), pokud již neexistuje.
* Přidání skupin souborů databáze k databázi, která obsahuje dělené fyzické soubory. 
* To lze provést pomocí [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx) Pokud nová nebo [ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx) Pokud databáze již existuje.
* Přidejte jeden nebo více souborů do každé skupiny souborů databáze (podle potřeby).
  
  > [!NOTE]
  > Zadejte cílové skupině souborů, která obsahuje data pro tento oddíl a názvy souborů fyzická databáze je ukládat data souborů.
  > 
  > 

Následující příklad vytvoří novou databázi pomocí tří skupin souborů jiné než primární a skupiny protokolu, který obsahuje jeden fyzický soubor v každém. Soubory databáze se vytvoří ve složce výchozí Data serveru SQL Server nakonfigurovaná v instanci systému SQL Server. Další informace o výchozích umístění souborů najdete v tématu [umístění souborů pro výchozí a název instance systému SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

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
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')

## <a name="create-a-partitioned-table"></a>Vytvořit tabulku oddílů
Vytvořit dělené tabulky podle schématu dat, mapované na skupiny souborů databáze, které vytvořili v předchozím kroku, musíte nejprve vytvořit funkci oddílu a schéma. Když jsou data hromadného importu do dělené tabulky, záznamy jsou rozděleny do skupin podle schématu oddílu, jak je popsáno níže.

### <a name="1-create-a-partition-function"></a>1. Vytvoření funkce oddílu
[Vytvořit funkci oddílu](https://msdn.microsoft.com/library/ms187802.aspx) této funkce definuje rozsah hodnot/hranic mají být zahrnuty v každé tabulce jednotlivých oddílů, například, chcete-li omezit oddílů podle kategorie month (některé\_data a času\_pole) v roce 2013:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

### <a name="2-create-a-partition-scheme"></a>2. Vytvořte schéma oddílu
[Vytvořte schéma oddílu](https://msdn.microsoft.com/library/ms179854.aspx). Toto schéma mapuje každý rozsah oddílu ve funkci oddílu fyzického skupině souborů, například:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Chcete-li ověření rozsahů v platnosti v každém oddílu podle funkce nebo schématu, spusťte následující dotaz:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

### <a name="3-create-a-partition-table"></a>3. Vytvořit tabulku oddílů
[Vytvoření dělenou tabulku](https://msdn.microsoft.com/library/ms174979.aspx)(s) podle schématu dat, a zadat oddíl schématu a omezení pole použitá při vytváření oddílů v tabulce, například:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Další informace najdete v tématu [vytvořit dělené tabulky a indexy](https://msdn.microsoft.com/library/ms188730.aspx).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Hromadný import dat pro každou tabulku jednotlivých oddílů

* Může používat BCP, BULK INSERT nebo jiné metody, jako [Průvodce migrací sady SQL Server](http://sqlazuremw.codeplex.com/). Poskytnutý příklad používá metodu BCP.
* [Mění databázi](https://msdn.microsoft.com/library/bb522682.aspx) Chcete-li změnit režim protokolování transakce BULK_LOGGED minimalizovat nároky na protokolování, například:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* K urychlení načítání dat, spuštění operace hromadného importu paralelně. Tipy k urychlení hromadného importu velkých objemů dat do databáze SQL serveru, naleznete v tématu [načtení 1TB za méně než 1 hodina](https://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

Následující příkaz powershellu je příkladem paralelních dat pomocí BCP načítají.

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


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Vytváření indexů pro optimalizaci spojení a výkon dotazů
* Pokud extrahovat data pro modelování z více tabulek, vytváření indexů na klíčích spojení ke zlepšení výkonu spojení.
* [Vytváření indexů](https://technet.microsoft.com/library/ms188783.aspx) (Clusterované či neclusterované) cílení na stejnou skupinu souborů pro každý oddíl, třeba:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  nebo,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Můžete k vytvoření indexů před hromadného importování dat. Vytvoření indexu před hromadný import může zpomalit načítání dat.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Proces pokročilých analýz a technologie v příkladu akce
Příklad začátku do konce Průvodce vědecké zpracování týmových dat pomocí veřejné datové sady, naleznete v tématu [vědecké zpracování týmových dat v akci: použití serveru SQL Server](sql-walkthrough.md).

