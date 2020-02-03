---
title: Přesun dat do virtuálního počítače s SQL serverem - vědecké zpracování týmových dat
description: Přesun dat z plochých souborů nebo z místního SQL serveru na SQL Server na virtuálním počítači Azure.
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
ms.openlocfilehash: b8a01b5f2f5ec64fea014468356408220f9c4f1a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721366"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Přesun dat do SQL Serveru na virtuálním počítači Azure

Tento článek popisuje možnosti pro přesun dat z plochých souborů (sdíleného svazku clusteru nebo TSV formáty) nebo z místního SQL serveru na SQL Server na virtuálním počítači Azure. Tyto úlohy pro přesun dat do cloudu jsou součástí vědecké zpracování týmových dat.

Téma, které popisuje možnosti přesunutí dat do Azure SQL Database pro Machine Learning, najdete v tématu [přesun dat do Azure SQL Database pro Azure Machine Learning](move-sql-azure.md).

Následující tabulka shrnuje možnosti pro přesun dat do SQL serveru na virtuálním počítači Azure.

| <b>Zdrojová</b> | <b>CÍL: SQL Server na virtuálním počítači Azure</b> |
| --- | --- |
| <b>Plochý soubor</b> |1. <a href="#insert-tables-bcp">Nástroj příkazového řádku pro hromadné kopírování (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">hromadné vložení dotazu SQL</a><br> 3. <a href="#sql-builtin-utilities">grafické integrované nástroje v SQL Server</a> |
| <b>Místní SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">nasazení databáze SQL Server do průvodce Microsoft Azure VM VM</a><br> 2. <a href="#export-flat-file">Export do plochého souboru</a><br> 3. <a href="#sql-migration">SQL Database Průvodce migrací</a> <br> 4. <a href="#sql-backup">zálohování a obnovení databáze</a><br> |

V tomto dokumentu se předpokládá, že příkazy SQL se spouštějí z SQL Server Management Studio nebo Průzkumníka databáze sady Visual Studio.

> [!TIP]
> Jako alternativu můžete použít [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) k vytvoření a naplánování kanálu, který přesune data do virtuálního počítače s SQL Server v Azure. Další informace najdete v tématu [kopírování dat pomocí Azure Data Factory (aktivita kopírování)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prereqs"></a>Požadavky
Tento kurz předpokládá, že máte:

* **Předplatné Azure**. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účet služby Azure Storage**. Pro ukládání dat v tomto kurzu použijete účet úložiště Azure. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Po vytvoření účtu úložiště, je potřeba získat klíč účtu pro přístup k úložišti. Viz [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).
* Zřízené **SQL Server na virtuálním počítači Azure**. Pokyny najdete v tématu [nastavení virtuálního počítače Azure SQL Server jako serveru IPython notebook pro pokročilou analýzu](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Instalace a konfigurace **Azure PowerShell** lokálně. Pokyny najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a>Přesunutí dat ze zdroje plochého souboru do SQL Server na virtuálním počítači Azure
Pokud vaše data jsou v plochého souboru (uspořádány ve formátu řádku nebo sloupce), se můžete přesunout do virtuálního počítače s SQL serverem v Azure pomocí následujících metod:

1. [Nástroj příkazového řádku pro hromadné kopírování (BCP)](#insert-tables-bcp)
2. [Hromadné vložení dotazu SQL](#insert-tables-bulkquery)
3. [Grafické integrované nástroje v SQL Server (import/export, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Nástroj příkazového řádku pro hromadné kopírování (BCP)
BCP je nástroj příkazového řádku nainstalovat s SQL serverem a je jedním z nejrychleji způsobů, jak přesunout data. Funguje v rámci všech tří SQL Server variant (místní SQL Server, SQL Azure a SQL Server virtuální počítač v Azure).

> [!NOTE]
> **Kde by měla být moje data pro BCP?**  
> I když není povinné, s soubory obsahující zdrojová data nachází ve stejném počítači jako cílový SQL Server umožňuje rychlejší přenos (sítě rychlosti vs. místní disk rychlost vstupně-výstupní operace). Ploché soubory, které obsahují data, můžete přesunout do počítače, kde se SQL Server nainstaluje pomocí různých nástrojů pro kopírování souborů, jako je [AzCopy](../../storage/common/storage-use-azcopy.md), [Průzkumník služby Azure Storage](https://storageexplorer.com/) nebo kopírování/vkládání přes protokol RDP (Remote Desktop Protocol) (RDP).
>
>

1. Ujistěte se, že se vytvoří databáze a tabulky na cílové databázi systému SQL Server. Tady je příklad, jak to udělat pomocí příkazů `Create Database` a `Create Table`:

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

> **Optimalizace vložení BCP** Pokud chcete optimalizovat taková vložení, přečtěte si následující článek s [pokyny pro optimalizaci hromadného importu](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) .
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Virtuálního vložení pro rychlejší přesun dat
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

### <a name="insert-tables-bulkquery"></a>Hromadné vložení dotazu SQL
[Hromadné vložení dotazu SQL](https://msdn.microsoft.com/library/ms188365) lze použít k importu dat do databáze ze souborů založených na řádcích nebo sloupcích (podporované typy jsou uvedeny v tématu[Příprava dat pro hromadné exporty nebo import (SQL Server)](https://msdn.microsoft.com/library/ms188609)).

Tady jsou některé ukázkové příkazy pro Bulk Insert se, jak je uvedeno níže:  

1. Analýza dat a nastavit všechny vlastní možnosti před importem, abyste měli jistotu, že databáze serveru SQL Server předpokládá stejný formát pro jakékoli zvláštní pole jako kalendářní data. Tady je příklad toho, jak nastavit formát data jako rok. měsíc den (Pokud data obsahují datum ve formátu rok měsíc dní):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Importujte data pomocí hromadného importu:

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

### <a name="sql-builtin-utilities"></a>Integrované nástroje v SQL Server
Pomocí služba SSIS (SQL Server Integration Services) (SSIS) můžete importovat data do SQL Server virtuálního počítače v Azure z plochého souboru.
Služby SSIS je k dispozici ve dvou studio prostředí. Podrobnosti najdete v tématu [integrační služby (SSIS) a prostředí studia](https://technet.microsoft.com/library/ms140028.aspx):

* Podrobnosti o SQL Server datových nástrojích najdete v tématu [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx) .  
* Podrobnosti o Průvodci importem a exportem najdete v tématu [Průvodce importem a exportem SQL Server](https://msdn.microsoft.com/library/ms141209.aspx) .

## <a name="sqlonprem_to_sqlonazurevm"></a>Přesun dat z místních SQL Server do SQL Server na virtuálním počítači Azure
Můžete použít také následující strategie migrace:

1. [Nasazení databáze SQL Server do průvodce Microsoft Azure VM VM](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportovat do plochého souboru](#export-flat-file)
3. [Průvodce migrací SQL Database](#sql-migration)
4. [Zálohování a obnovení databáze](#sql-backup)

Popisujeme všechny tyto možnosti:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Nasazení databáze SQL serveru do Průvodce vytvořením virtuálního počítače Microsoft Azure
**Průvodce nasazením databáze SQL Server do virtuálního počítače s Microsoft Azure** je jednoduchý a doporučený způsob, jak přesunout data z místní instance SQL Server do SQL Server na virtuálním počítači Azure. Podrobné pokyny a další informace o dalších alternativách najdete v tématu [migrace databáze do SQL Server na virtuálním počítači Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportovat do plochého souboru
K hromadnému exportu dat z místních SQL Server, jak je popsáno v tématu [Hromadný import a export dat (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) , lze použít různé metody. Tento dokument se týká Program hromadného kopírování (BCP) jako příklad. Jakmile se data se exportují do plochého souboru, můžete importovat na jiný SQL server pomocí hromadného importu.

1. Exportovat data z místního SQL serveru do souboru následujícím způsobem použití nástroje bcp

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Vytvořte databázi a tabulku na SQL Serverm VIRTUÁLNÍm počítači v Azure pomocí `create database` a `create table` pro schéma tabulky exportované v kroku 1.
3. Vytvořte soubor formátu pro popis schématu tabulky se importovat/exportovat data. Podrobnosti o formátovém souboru jsou popsány v tématu [Create a Format File (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formátování generování souboru při spuštění BCP z počítače systému SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Formátování generování souboru při spuštění BCP vzdáleně s SQL serverem

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Pomocí kterékoli z metod popsaných v části [přesun dat ze zdroje souborů](#filesource_to_sqlonazurevm) přesuňte data z plochých souborů do SQL Server.

### <a name="sql-migration"></a>Průvodce migrací SQL Database
[Průvodce migrací databáze SQL Server](https://sqlazuremw.codeplex.com/) nabízí uživatelsky přívětivý způsob, jak přesouvat data mezi dvěma INSTANCEMI systému SQL Server. Umožňuje uživateli mapování schématu dat mezi zdroji a cílové tabulky, zvolte typy sloupců a různé další funkce. Hromadné kopírování (BCP) na pozadí používá. Snímek obrazovky s úvodní obrazovce Průvodce migrací databáze SQL je uveden níže.  

![Průvodce migrací sady SQL Server][2]

### <a name="sql-backup"></a>Zálohování a obnovení databáze
Systém SQL Server podporuje:

1. [Funkce zálohování a obnovení databáze](https://msdn.microsoft.com/library/ms187048.aspx) (pro místní soubor nebo BacPac export do objektů BLOB) a [aplikace datové vrstvy](https://msdn.microsoft.com/library/ee210546.aspx) (pomocí BacPac).
2. Schopnost vytvářet virtuální počítače s SQL serverem v Azure s zkopírovaný databází nebo zkopírovat do existující databázi SQL Azure. Další informace najdete v tématu [použití Průvodce kopírováním databáze](https://msdn.microsoft.com/library/ms188664.aspx).

Snímek databáze back up/obnovení možnosti systému SQL Server Management Studio najdete níž.

![Nástroj pro Import SQL serveru][1]

## <a name="resources"></a>Zdroje a prostředky
[Migrace databáze na SQL Server na virtuálním počítači Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[SQL Server na Azure Virtual Machines – přehled](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
