---
title: Přesun dat do systému SQL Server na virtuální počítač Azure | Microsoft Docs
description: Přesun dat z plochých souborů nebo z místního serveru SQL do systému SQL Server na virtuálním počítači Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 2c9ef1d3-4f5c-4b1f-bf06-223646c8af06
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 229e2c07a3e8d83fc01dc5f1542fd250cb4678f7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838972"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Přesun dat do SQL Serveru na virtuálním počítači Azure
Toto téma popisuje možnosti pro přesun dat z plochých souborů (formáty CSV nebo TSV) nebo z místního serveru SQL do systému SQL Server na virtuální počítač Azure. Tyto úlohy pro přesun dat do cloudu jsou součástí procesu Team dat vědecké účely.

Téma, které popisuje možnosti pro přesun dat do Azure SQL Database pro Machine Learning, najdete v části [přesun dat do Azure SQL Database pro Azure Machine Learning](move-sql-azure.md).

**Nabídky** pod odkazy na témata, které popisují, jak ingestovat data do jiných cíl prostředích, kde můžete data ukládat a zpracovávat během tým datové vědy procesu (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

Následující tabulka shrnuje možnosti pro přesun dat do systému SQL Server na virtuální počítač Azure.

| <b>ZDROJ</b> | <b>Cíl: SQL Server na virtuální počítač Azure</b> |
| --- | --- |
| <b>Plochý soubor</b> |1. <a href="#insert-tables-bcp">Nástroj příkazového řádku pro hromadné kopírování (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">Hromadné vložení SQL dotazu </a><br> 3. <a href="#sql-builtin-utilities">Grafické nástroje integrovanou v systému SQL Server</a> |
| <b>Místní SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Nasazení databáze systému SQL Server do Průvodce virtuálních počítačů služby Microsoft Azure</a><br> 2. <a href="#export-flat-file">Exportovat do plochý soubor </a><br> 3. <a href="#sql-migration">Průvodce migrací databáze SQL </a> <br> 4. <a href="#sql-backup">Databáze back up a obnovení </a><br> |

Všimněte si, že tento dokument předpokládá, že příkazy SQL budou provedeny z SQL Server Management Studio nebo Visual Studio Průzkumník databáze.

> [!TIP]
> Jako alternativu, můžete použít [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) k vytváření a plánování kanál, který bude přesouvat data virtuálního počítače s SQL Server na platformě Azure. Další informace najdete v tématu [kopírování dat pomocí Azure Data Factory (aktivita kopírování)](../../data-factory/v1/data-factory-data-movement-activities.md).
>
>

## <a name="prereqs"></a>Požadavky
Tento kurz předpokládá, že máte:

* **Předplatné**. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účtu úložiště Azure**. Pro ukládání dat v tomto kurzu budete používat účet úložiště Azure. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Po vytvoření účtu úložiště, musíte získat klíč účtu, který používá pro přístup k úložišti. V tématu [Správa přístupových klíčů úložiště](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Zřízení **systému SQL Server ve virtuálním počítači Azure**. Pokyny najdete v tématu [nastavení se virtuální počítač Azure SQL Server jako server IPython Poznámkový blok pro pokročilou analýzu](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Nainstalovaný a nakonfigurovaný **prostředí Azure PowerShell** místně. Pokyny najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a> Přesun dat z plochých souborů zdroje k systému SQL Server na virtuálním počítači Azure
Pokud vaše data v plochý soubor (seřazeny ve formátu řádku nebo sloupce), se můžete přesunout na virtuální počítač SQL Server na platformě Azure pomocí následujících metod:

1. [Nástroj příkazového řádku pro hromadné kopírování (BCP)](#insert-tables-bcp)
2. [Hromadné vložení SQL dotazu ](#insert-tables-bulkquery)
3. [Grafické nástroje integrovanou v systému SQL Server (importu a exportu, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Nástroj příkazového řádku pro hromadné kopírování (BCP)
Je nainstalovaný se systémem SQL Server nástroj příkazového řádku BCP a je nejrychlejší způsob pro přesun dat. Funguje v různých všechny tři varianty systému SQL Server (místní SQL Server, SQL Azure a virtuální počítač s SQL serverem v Azure).

> [!NOTE]
> **Kam mají být data pro BCP?**  
> Když to není nutné, s soubory obsahující zdroje dat umístěných na stejném počítači jako cíl systému SQL Server umožňuje rychlejší přenosů (sítě rychlost vs místní disk vstupně-výstupní operace rychlost). Ploché soubory obsahující data do počítače můžete přesunout tam, kde je nainstalován systém SQL Server pomocí různých kopírování souborů nástroje jako [AZCopy](../../storage/common/storage-use-azcopy.md), [Azure Storage Explorer](http://storageexplorer.com/) nebo windows, kopírování a vkládání přes vzdálenou plochu Protokol (RDP).
>
>

1. Ujistěte se, že databáze a tabulky jsou vytvořeny v cílové databázi systému SQL Server. Tady je příklad, jak to provést, že pomocí `Create Database` a `Create Table` příkazy:

        CREATE DATABASE <database_name>

        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        )
2. Generujte soubor formátu, který popisuje schéma pro tabulku po vydání následujícího příkazu z příkazového řádku na počítači nainstalovanou bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
3. Vložení dat do databáze pomocí příkazu bcp následujícím způsobem. Tento postup měl fungovat z příkazového řádku za předpokladu, že SQL Server je nainstalován ve stejném počítači:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optimalizace BCP vloží** naleznete v následujícím článku ["Pokyny pro optimalizaci hromadným importem"](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) za účelem optimalizace takové vložení.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Paralelním prováděním vložení pro rychlejší přesun dat
Pokud přesouváte data velká, můžete urychlit věcí současně spuštěním více BCP příkazů paralelně ve skriptu prostředí PowerShell.

> [!NOTE]
> **Velké objemy dat přijímání** za účelem optimalizace načítání pro velké a velké datové sady dat, oddílu logické a fyzické databázových tabulek pomocí více skupin souborů a oddíl tabulky. Další informace o vytvoření a načtení dat do oddílu tabulky najdete v tématu [paralelní tabulky oddílů SQL zatížení](parallel-load-sql-partitioned-tables.md).
>
>

Ukázkový skript prostředí PowerShell níže ukazují paralelní vloží při použití nástroje bcp:

    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
      }


    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }


    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }

    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Hromadné vložení SQL dotazu
[Hromadné vložení dotazu SQL](https://msdn.microsoft.com/library/ms188365) lze použít k importu dat do databáze z řádku nebo sloupce na základě souborů (podporované typy jsou popsané v[Příprava Data pro hromadné exportu nebo importu (SQL Server)](https://msdn.microsoft.com/library/ms188609)) tématu.

Tady jsou některé ukázkové příkazy pro hromadné vložení se, jak je uvedeno níže:  

1. Analyzovat data a nastavte všechny vlastní možnosti před importem a ujistěte se, že databázi systému SQL Server předpokládá stejný formát pro všechny speciální pole, jako je například kalendářní data. Tady je příklad toho, jak nastavit formát data jako rok měsíc den (Pokud data obsahují datum ve formátu rok měsíc den):

        SET DATEFORMAT ymd;    
2. Importujte dat pomocí hromadného importu příkazy:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )

### <a name="sql-builtin-utilities"></a>Integrované nástroje SQL serveru
Integrace služby SSIS (SQL Server) můžete použít k importu dat do virtuální počítač s SQL serverem na Azure z plochého souboru.
Služby SSIS je k dispozici ve dvou studio prostředí. Podrobnosti najdete v tématu [Integration Services (SSIS) a prostředí Studio](https://technet.microsoft.com/library/ms140028.aspx):

* Podrobnosti na SQL Server Data Tools najdete v tématu [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Informace o Průvodci importem a exportem v [Microsoft SQL Server importovat a exportovat Průvodce](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Přesun dat z místního SQL serveru do systému SQL Server ve virtuálním počítači Azure
Můžete také použít následující strategie migrace:

1. [Nasazení databáze systému SQL Server do Průvodce virtuálních počítačů služby Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportovat do plochý soubor](#export-flat-file)
3. [Průvodce migrací databáze SQL](#sql-migration)
4. [Databáze back up a obnovení](#sql-backup)

Jsme popisují každou z těchto níže:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Nasazení databáze systému SQL Server do Průvodce virtuálních počítačů služby Microsoft Azure
**Nasazení databáze systému SQL Server do virtuálního počítače Microsoft Azure průvodce** je jednoduchý a doporučený způsob pro přesun dat z místní instance systému SQL Server do systému SQL Server na virtuálním počítači Azure. Podrobné kroky a také se zabývat náhradních řešení, najdete v části [migrace databáze do systému SQL Server na virtuálním počítači Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportovat do plochý soubor
Chcete-li hromadně exportu dat z SQL serveru místní, jak je uvedeno v lze použít různé metody [hromadný Import a Export dat (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) tématu. Tento dokument se týká Program hromadného kopírování (BCP) jako příklad. Jakmile data se exportují do plochý soubor, lze importovat na jiný server SQL pomocí hromadného importu.

1. Exportovat data z místního SQL serveru do souboru pomocí nástroje bcp takto

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Vytvořit databázi a tabulku na virtuální počítač s SQL serverem na Azure pomocí `create database` a `create table` pro schéma tabulky exportovali v kroku 1.
3. Vytvořte soubor ve formátu pro popisující schématu tabulky se exportovat nebo importovat data. Podrobnosti o formátu souboru jsou popsané v [vytvořit soubor ve formátu (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Generování souboru formátu při spuštění BCP z počítače systému SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Generování souboru formátu při spuštění BCP vzdáleně s SQL serverem

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Použít některou z metod popsaných v části [přesun dat ze souboru zdroje](#filesource_to_sqlonazurevm) pro přesun dat v ploché soubory do systému SQL Server.

### <a name="sql-migration"></a>Průvodce migrací databáze SQL
[Průvodce migrací databáze SQL serveru](http://sqlazuremw.codeplex.com/) obsahuje uživatelsky přívětivé pro přesun dat mezi dvě instance serveru SQL. Umožňuje uživateli mapování schéma dat mezi zdroji a cílovou tabulku, zvolte typy sloupců a různé další funkce. Hromadné kopírování (BCP) skrytě používá. Snímek obrazovky na úvodní obrazovce Průvodce migrací databáze SQL jsou uvedeny níže.  

![Průvodce migrací serveru SQL][2]

### <a name="sql-backup"></a>Databáze back up a obnovení
Systém SQL Server podporuje:

1. [Zálohu databáze zpět a obnovit funkčnost](https://msdn.microsoft.com/library/ms187048.aspx) (i do místního souboru nebo souboru bacpac exportovat do objektu blob) a [aplikace datové vrstvy](https://msdn.microsoft.com/library/ee210546.aspx) (pomocí souboru bacpac).
2. Schopnost přímo v Azure vytvořit virtuální počítače serveru SQL s zkopírovaný databáze nebo kopírovat do existující databázi SQL Azure. Další podrobnosti najdete v tématu [pomocí Průvodce kopírováním databáze](https://msdn.microsoft.com/library/ms188664.aspx).

Snímek obrazovky zálohování databáze nebo obnovit možnosti z SQL Server Management Studio jsou uvedeny níže.

![Nástroj pro Import serveru SQL][1]

## <a name="resources"></a>Zdroje a prostředky
[Migrace databáze do systému SQL Server ve virtuálním počítači Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[SQL Server na Azure Virtual Machines – přehled](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
