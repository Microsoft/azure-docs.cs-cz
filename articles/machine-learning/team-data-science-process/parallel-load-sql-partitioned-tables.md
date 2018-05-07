---
title: Sestavení a optimalizovat tabulky pro rychlé paralelní import dat do systému SQL Server na virtuálním počítači Azure | Microsoft Docs
description: Paralelní hromadný import dat pomocí tabulek oddílů SQL
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ff90fdb0-5bc7-49e8-aee7-678b54f901c8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: 55c213e26d39f1f9c27fdeae89e651a5f99a98b5
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>Paralelní hromadný import dat pomocí tabulek oddílů SQL
Tento dokument popisuje, jak sestavit dělené tabulky pro rychlé paralelní hromadný import dat do databáze systému SQL Server. Pro velké objemy dat načítání nebo přenos k databázi SQL, import dat do databáze SQL a následné dotazy lze zvýšit pomocí *rozdělena na oddíly tabulky a zobrazení*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Vytvořit novou databázi a sadu skupin souborů
* [Vytvořit novou databázi](https://technet.microsoft.com/library/ms176061.aspx), pokud již neexistuje.
* Přidejte skupiny souborů databáze pro databázi, která obsahuje oddílů fyzické soubory. 
* To lze provést pomocí [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx) Pokud nové nebo [ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx) Pokud databáze již existuje.
* Přidejte jeden nebo více souborů (podle potřeby) do každé skupině souborů databáze.
  
  > [!NOTE]
  > Zadejte cílové skupiny souborů, který obsahuje data pro tento oddíl a názvy souborů fyzická databáze, které jsou uložená data souborů.
  > 
  > 

Následující příklad vytvoří novou databázi pomocí tří skupin souborů jiné než primární a skupiny protokolu obsahující jeden fyzický soubor v každé. Soubory databáze jsou vytvářeny dat systému SQL Server do výchozí složky jako nakonfigurovaný v instanci systému SQL Server. Další informace o výchozí umístění souborů najdete v tématu [umístění souborů pro výchozí a s názvem instance systému SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

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

## <a name="create-a-partitioned-table"></a>Vytvoření oddílů tabulky
K vytvoření oddílů tabulky či tabulek podle schématu dat, mapované na skupiny souborů databáze, vytvořili v předchozím kroku, musíte nejprve vytvořit funkce oddílu a schématu. Když data hromadně importovat do oddílů tabulky či tabulek, záznamy jsou rozděleny do skupin podle schéma oddílu, jak je popsáno níže.

### <a name="1-create-a-partition-function"></a>1. Vytvořit funkci oddílu
[Vytvořit funkci oddílu](https://msdn.microsoft.com/library/ms187802.aspx) tuto funkci definuje rozsah hodnot nebo hranic mají být zahrnuty v každé tabulce jednotlivých oddílu, například můžete omezit oddíly po měsících (některé\_data a času\_pole) roku 2013:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

### <a name="2-create-a-partition-scheme"></a>2. Vytvořit schéma oddílu
[Vytvořit schéma oddílu](https://msdn.microsoft.com/library/ms179854.aspx). Toto schéma mapuje každý oddíl rozsah ve funkci oddílu fyzického souborů, například:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Pokud chcete ověřit rozsahům výsledkem bude každý oddíl podle funkce nebo schéma, spusťte následující dotaz:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

### <a name="3-create-a-partition-table"></a>3. Vytvoří tabulku oddílů
[Vytvoření oddílů tabulky](https://msdn.microsoft.com/library/ms174979.aspx)(s) podle vašeho schématu dat a zadejte oddílu schématu a omezení pole použité při vytváření oddílů v tabulce, například:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Další informace najdete v tématu [vytvoření tabulky rozdělena na oddíly a indexy](https://msdn.microsoft.com/library/ms188730.aspx).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Hromadný import dat pro každou tabulku jednotlivých oddílu

* Můžete použít BCP, BULK INSERT nebo jiné metody, jako [Průvodce migrací serveru SQL](http://sqlazuremw.codeplex.com/). Ukázkový používá metodu BCP.
* [Provádění změn v databázi](https://msdn.microsoft.com/library/bb522682.aspx) ke změně schématu protokolování transakcí BULK_LOGGED k minimalizaci režie protokolování, například:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Cílem urychlit jejich nahrávání dat, spusťte hromadné operace importu paralelně. Tipy k urychlení hromadný import velkých objemů dat do databáze systému SQL Server, najdete v tématu [načíst 1TB za méně než 1 hodinu](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

Následující skript prostředí PowerShell je příklad paralelní dat pomocí BCP načítání.

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


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Vytvářejte indexy k optimalizaci spojení a výkon dotazů
* Pokud extrahování dat pro modelování z více tabulek, vytvořte na klíče připojení ke zlepšení výkonu spojení indexy.
* [Vytvářejte indexy](https://technet.microsoft.com/library/ms188783.aspx) (Clusterované či neclusterované) cílení na stejnou skupinu souborů pro každý oddíl pro například:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  Nebo,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Můžete vytvořit indexy před hromadný import data. Vytvoření indexu před hromadný import zpomaluje načítání dat.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Proces pokročilou analýzu a technologie v příkladu akce
Příklad začátku do konce návod, vědecké účely procesu Team dat pomocí veřejné datové sady, naleznete v části [proces vědecké účely Team dat v akci: pomocí SQL serveru](sql-walkthrough.md).

