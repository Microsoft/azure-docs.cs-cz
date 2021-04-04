---
title: Přesun dat na SQL Server virtuální počítač – proces týmu pro datové vědy
description: Přesuňte data z plochých souborů nebo místních SQL Server a SQL Server na virtuálním počítači Azure.
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
ms.openlocfilehash: c80a90b07e25942e751d52cafa47f6e3e94852ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320328"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Přesun dat do SQL Serveru na virtuálním počítači Azure

Tento článek popisuje možnosti přesunu dat buď z plochých souborů (formáty CSV nebo TSV), nebo z místního SQL Server pro SQL Server na virtuálním počítači Azure. Tyto úlohy pro přesun dat do cloudu jsou součástí vědeckého procesu týmového zpracování dat.

Téma, které popisuje možnosti přesunutí dat do Azure SQL Database pro Machine Learning, najdete v tématu [přesun dat do Azure SQL Database pro Azure Machine Learning](move-sql-azure.md).

Následující tabulka shrnuje možnosti přesunutí dat na SQL Server na virtuálním počítači Azure.

| <b>Zdrojová</b> | <b>CÍL: SQL Server na virtuálním počítači Azure</b> |
| --- | --- |
| <b>Plochý soubor</b> |1. <a href="#insert-tables-bcp">Nástroj příkazového řádku pro hromadné kopírování (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">hromadné vložení dotazu SQL </a><br> 3. <a href="#sql-builtin-utilities">grafické integrované nástroje v SQL Server</a> |
| <b>Místní SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">nasazení databáze SQL Server do průvodce Microsoft Azure VM VM</a><br> 2. <a href="#export-flat-file">Export do plochého souboru </a><br> 3. <a href="#sql-migration">SQL Database Průvodce migrací </a> <br> 4. <a href="#sql-backup">zálohování a obnovení databáze </a><br> |

V tomto dokumentu se předpokládá, že příkazy SQL se spouštějí z SQL Server Management Studio nebo Průzkumníka databáze sady Visual Studio.

> [!TIP]
> Jako alternativu můžete použít [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) k vytvoření a naplánování kanálu, který přesune data do virtuálního počítače s SQL Server v Azure. Další informace najdete v tématu [kopírování dat pomocí Azure Data Factory (aktivita kopírování)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Požadavky
V tomto kurzu se předpokládá, že máte následující:

* **Předplatné Azure** Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účet služby Azure Storage**. K uložení dat v tomto kurzu použijete účet služby Azure Storage. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Po vytvoření účtu úložiště budete muset získat klíč účtu, který se používá pro přístup k úložišti. Viz [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).
* Zřízené **SQL Server na virtuálním počítači Azure**. Pokyny najdete v tématu [nastavení virtuálního počítače Azure SQL Server jako serveru IPython notebook pro pokročilou analýzu](../data-science-virtual-machine/overview.md).
* Instalace a konfigurace **Azure PowerShell** lokálně. Pokyny najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a> Přesunutí dat ze zdroje plochého souboru do SQL Server na virtuálním počítači Azure
Pokud jsou vaše data v nestrukturovaném souboru (uspořádaná ve formátu řádku nebo sloupce), můžete ji přesunout do SQL Server virtuálního počítače v Azure pomocí následujících metod:

1. [Nástroj příkazového řádku pro hromadné kopírování (BCP)](#insert-tables-bcp)
2. [Hromadné vložení dotazu SQL](#insert-tables-bulkquery)
3. [Grafické integrované nástroje v SQL Server (import/export, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Nástroj příkazového řádku pro hromadné kopírování (BCP)
BCP je nástroj příkazového řádku nainstalovaný s SQL Server a je jedním z nejrychlejších způsobů, jak přesouvat data. Funguje v rámci všech tří SQL Server variant (místní SQL Server, SQL Azure a SQL Server virtuální počítač v Azure).

> [!NOTE]
> **Kde by měla být moje data pro BCP?**  
> I když to není nutné, soubory, které obsahují zdrojová data umístěná na stejném počítači jako cílový SQL Server, umožňují rychlejší přenos (rychlost sítě vs. místní disk). Ploché soubory, které obsahují data, můžete přesunout do počítače, kde se SQL Server nainstaluje pomocí různých nástrojů pro kopírování souborů, jako je [AzCopy](../../storage/common/storage-use-azcopy-v10.md), [Průzkumník služby Azure Storage](https://storageexplorer.com/) nebo kopírování/vkládání přes protokol RDP (Remote Desktop Protocol) (RDP).
>
>

1. Ujistěte se, že databáze a tabulky jsou vytvořeny v cílové SQL Server databázi. Tady je příklad, jak to udělat pomocí `Create Database` `Create Table` příkazů a:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Vygenerujte formátový soubor, který popisuje schéma pro tabulku, a to spuštěním následujícího příkazu z příkazového řádku počítače, kde je nainstalován BCP.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Vložte data do databáze pomocí příkazu BCP, který by měl fungovat z příkazového řádku, pokud je SQL Server nainstalovaná na stejném počítači:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Optimalizace vložení BCP** Pokud chcete optimalizovat taková vložení, přečtěte si následující článek s [pokyny pro optimalizaci hromadného importu](/previous-versions/sql/sql-server-2008-r2/ms177445(v=sql.105)) .
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Virtuálního vložení pro rychlejší přesun dat
Pokud jsou data, která přesouváte, Velká, můžete urychlit současné spouštění více příkazů BCP ve skriptu PowerShellu.

> [!NOTE]
> Ingestování velkých objemů **dat** Pro optimalizaci načítání dat pro velké a velmi velké datové sady vytvořte oddíly logických a fyzických databázových tabulek pomocí více skupin souborů a tabulek oddílů. Další informace o vytváření a načítání dat pro tabulky oddílů najdete v tématu [paralelní načítání tabulek oddílů SQL](parallel-load-sql-partitioned-tables.md).
>
>

Následující vzorový skript PowerShellu ukazuje paralelní vložení pomocí BCP:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
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
```

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>Hromadné vložení dotazu SQL
[Hromadné vložení dotazu SQL](/sql/t-sql/statements/bulk-insert-transact-sql) lze použít k importu dat do databáze ze souborů založených na řádcích nebo sloupcích (podporované typy jsou uvedeny v tématu[Příprava dat pro hromadné exporty nebo import (SQL Server)](/sql/relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server)).

Tady je několik ukázkových příkazů pro hromadné vložení:  

1. Analyzujte data a před importem nastavte všechny vlastní možnosti, abyste měli jistotu, že SQL Server databáze předpokládá stejný formát pro všechna speciální pole, jako jsou kalendářní data. Tady je příklad, jak nastavit formát data jako rok-měsíc (Pokud vaše data obsahují datum v roce-měsíc-den):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Importovat data pomocí příkazů hromadného importu:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Integrované nástroje v SQL Server
Pomocí služba SSIS (SQL Server Integration Services) (SSIS) můžete importovat data do SQL Server virtuálního počítače v Azure z plochého souboru.
SSIS je k dispozici ve dvou prostředích studia. Podrobnosti najdete v tématu [integrační služby (SSIS) a prostředí studia](/sql/integration-services/integration-services-ssis-development-and-management-tools):

* Podrobnosti o SQL Server datových nástrojích najdete v tématu [Microsoft SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) .  
* Podrobnosti o Průvodci importem a exportem najdete v tématu [Průvodce importem a exportem SQL Server](/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard) .

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Přesun dat z místních SQL Server do SQL Server na virtuálním počítači Azure
Můžete také použít následující strategie migrace:

1. [Nasazení databáze SQL Server do průvodce Microsoft Azure VM VM](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportovat do plochého souboru](#export-flat-file)
3. [Průvodce migrací SQL Database](#sql-migration)
4. [Zálohování a obnovení databáze](#sql-backup)

Popisujeme všechny tyto možnosti:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Nasazení databáze SQL Server do průvodce Microsoft Azure VM VM
**Průvodce nasazením databáze SQL Server do virtuálního počítače s Microsoft Azure** je jednoduchý a doporučený způsob, jak přesunout data z místní instance SQL Server do SQL Server na virtuálním počítači Azure. Podrobné pokyny a další informace o dalších alternativách najdete v tématu [migrace databáze do SQL Server na virtuálním počítači Azure](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md).

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportovat do plochého souboru
K hromadnému exportu dat z místních SQL Server, jak je popsáno v tématu [Hromadný import a export dat (SQL Server)](/sql/relational-databases/import-export/bulk-import-and-export-of-data-sql-server) , lze použít různé metody. V tomto dokumentu se jako příklad pokryje program hromadného kopírování (BCP). Jakmile budou data exportována do plochého souboru, lze je pomocí hromadného importu importovat do jiného systému SQL Server.

1. Exportujte data z místních SQL Server do souboru pomocí nástroje BCP následujícím způsobem.

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Vytvořte databázi a tabulku na SQL Serverm VIRTUÁLNÍm počítači v Azure pomocí `create database` a `create table` pro schéma tabulky exportované v kroku 1.
3. Vytvořte formátový soubor pro popis schématu tabulky exportovaných a importovaných dat. Podrobnosti o formátovém souboru jsou popsány v tématu [Create a Format File (SQL Server)](/sql/relational-databases/import-export/create-a-format-file-sql-server).

    Při spuštění nástroje BCP z SQL Server počítače se naformátuje generování souboru

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

    Formátovat generaci souborů při vzdáleném spuštění BCP proti SQL Server

    `bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n`
4. Pomocí kterékoli z metod popsaných v části [přesun dat ze zdroje souborů](#filesource_to_sqlonazurevm) přesuňte data z plochých souborů do SQL Server.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>Průvodce migrací SQL Database
[Průvodce migrací databáze SQL Server](https://sqlazuremw.codeplex.com/) nabízí uživatelsky přívětivý způsob, jak přesouvat data mezi dvěma INSTANCEMI systému SQL Server. Umožňuje uživateli mapovat data schématu mezi zdrojovými a cílovými tabulkami, zvolit typy sloupců a různé další funkce. Používá hromadné kopírování (BCP) v rámci pokrývání. Snímek obrazovky úvodní obrazovky Průvodce migrací SQL Database je uvedený níže.  

![Průvodce migrací SQL Server][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Zálohování a obnovení databáze
SQL Server podporuje:

1. [Funkce zálohování a obnovení databáze](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) (pro místní soubor nebo BacPac export do objektů BLOB) a [aplikace datové vrstvy](/sql/relational-databases/data-tier-applications/data-tier-applications) (pomocí BacPac).
2. Možnost přímého vytváření SQL Server virtuálních počítačů v Azure pomocí zkopírované databáze nebo kopírování do existující databáze v SQL Database. Další informace najdete v tématu [použití Průvodce kopírováním databáze](/sql/relational-databases/databases/use-the-copy-database-wizard).

Snímek obrazovky možností zálohování a obnovení databáze z SQL Server Management Studio je uveden níže.

![Nástroj pro import SQL Server][1]

## <a name="resources"></a>Zdroje informací
[Migrace databáze na SQL Server na virtuálním počítači Azure](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md)

[SQL Server na Azure Virtual Machines – přehled](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png