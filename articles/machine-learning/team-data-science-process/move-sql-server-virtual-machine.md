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

Téma, které popisuje možnosti pro přesun dat do služby Azure SQL Database pro Machine Learning, naleznete v tématu [přesun dat do služby Azure SQL Database pro Azure Machine Learning](move-sql-azure.md).

Následující tabulka shrnuje možnosti pro přesun dat do SQL serveru na virtuálním počítači Azure.

| <b>ZDROJ</b> | <b>Cíl: SQL Server na virtuálním počítači Azure</b> |
| --- | --- |
| <b>Plochý soubor</b> |1. <a href="#insert-tables-bcp">Nástroj příkazového řádku pro hromadné kopírování (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">hromadné vložení dotazu SQL</a><br> 3. <a href="#sql-builtin-utilities">grafické integrované nástroje v SQL Server</a> |
| <b>Na místním SQL serveru</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">nasazení databáze SQL Server do průvodce Microsoft Azure VM VM</a><br> 2. <a href="#export-flat-file">Export do plochého souboru</a><br> 3. <a href="#sql-migration">SQL Database Průvodce migrací</a> <br> 4. <a href="#sql-backup">zálohování a obnovení databáze</a><br> |

V tomto dokumentu se předpokládá, že příkazy SQL se spouštějí z SQL Server Management Studio nebo Průzkumníka databáze sady Visual Studio.

> [!TIP]
> Jako alternativu můžete použít [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) k vytvoření a naplánování kanálu, který bude přesun dat do virtuálního počítače s SQL serverem v Azure. Další informace najdete v tématu [kopírování dat pomocí služby Azure Data Factory (aktivita kopírování)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prereqs"></a>Požadavky
Tento kurz předpokládá, že máte:

* **Předplatného Azure**. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účtu služby Azure storage**. Pro ukládání dat v tomto kurzu použijete účet úložiště Azure. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Po vytvoření účtu úložiště, je potřeba získat klíč účtu pro přístup k úložišti. Viz [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).
* Zřízení **systému SQL Server na Virtuálním počítači Azure**. Pokyny najdete v tématu [nastavení virtuálního počítače s Azure SQL serveru jako serveru IPython Notebook pro pokročilé analýzy](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Nainstalovaný a nakonfigurovaný **prostředí Azure PowerShell** místně. Pokyny najdete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a> Přesouvá data z plochého souboru použitému jako zdroj pro SQL Server na Virtuálním počítači Azure
Pokud vaše data jsou v plochého souboru (uspořádány ve formátu řádku nebo sloupce), se můžete přesunout do virtuálního počítače s SQL serverem v Azure pomocí následujících metod:

1. [Nástroj příkazového řádku pro hromadné kopírování (BCP)](#insert-tables-bcp)
2. [Dotaz SQL pro hromadné vložení](#insert-tables-bulkquery)
3. [Grafické nástroje integrované v SQL serveru (Import/Export, služby SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Nástroj příkazového řádku pro hromadné kopírování (BCP)
BCP je nástroj příkazového řádku nainstalovat s SQL serverem a je jedním z nejrychleji způsobů, jak přesunout data. Funguje v rámci všech tří SQL Server variant (místní SQL Server, SQL Azure a SQL Server virtuální počítač v Azure).

> [!NOTE]
> **Kdy mají být data pro BCP?**  
> I když není povinné, s soubory obsahující zdrojová data nachází ve stejném počítači jako cílový SQL Server umožňuje rychlejší přenos (sítě rychlosti vs. místní disk rychlost vstupně-výstupní operace). Ploché soubory obsahující data do počítače můžete přesunout tam, kde je nainstalován SQL Server pomocí různých kopírování souborů nástroje jako [AZCopy](../../storage/common/storage-use-azcopy.md), [Průzkumníka služby Azure Storage](https://storageexplorer.com/) nebo windows, kopírování a vkládání přes vzdálenou plochu Protocol (RDP).
>
>

1. Ujistěte se, že se vytvoří databáze a tabulky na cílové databázi systému SQL Server. Tady je příklad tohoto postupu, že při použití `Create Database` a `Create Table` příkazy:

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

> **Optimalizace BCP vloží** najdete v následujícím článku ["Pokyny pro optimalizaci hromadný Import"](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) optimalizovat tyto operace vložení.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Paralelní provádění operace vložení pro rychlejší přesun dat
Pokud jsou data, která přesouváte, Velká, můžete urychlit současné spouštění více příkazů BCP ve skriptu PowerShellu.

> [!NOTE]
> Ingestování velkých objemů **dat** Pro optimalizaci načítání dat pro velké a velmi velké datové sady vytvořte oddíly logických a fyzických databázových tabulek pomocí více skupin souborů a tabulek oddílů. Další informace o vytváření a načítání dat do tabulek oddílů SQL najdete v tématu [tabulek oddílů SQL paralelní zatížení](parallel-load-sql-partitioned-tables.md).
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

### <a name="insert-tables-bulkquery"></a>Dotaz SQL pro hromadné vložení
[Hromadné vložení dotaz SQL](https://msdn.microsoft.com/library/ms188365) lze použít k importu dat do databáze z řádku/sloupce na základě souborů (podporované typy jsou popsané v[připravit Data pro hromadné Export nebo Import (SQL Server)](https://msdn.microsoft.com/library/ms188609)) tématu.

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

### <a name="sql-builtin-utilities"></a>Integrované nástroje SQL serveru
Pomocí služba SSIS (SQL Server Integration Services) (SSIS) můžete importovat data do SQL Server virtuálního počítače v Azure z plochého souboru.
Služby SSIS je k dispozici ve dvou studio prostředí. Podrobnosti najdete v tématu [Integration Services (SSIS) a Studio prostředí](https://technet.microsoft.com/library/ms140028.aspx):

* Podrobnosti o SQL Server Data Tools, naleznete v tématu [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Podrobnosti v průvodci Import/Export najdete v tématu [průvodce Export a Import SQL serveru](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Přesun dat z místního SQL serveru na SQL Server na Virtuálním počítači Azure
Můžete použít také následující strategie migrace:

1. [Nasazení databáze SQL serveru do Průvodce vytvořením virtuálního počítače Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportovat do plochých souborů](#export-flat-file)
3. [Průvodce migrací služby SQL Database](#sql-migration)
4. [Databáze back up a obnovení](#sql-backup)

Popisujeme všechny tyto možnosti:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Nasazení databáze SQL serveru do Průvodce vytvořením virtuálního počítače Microsoft Azure
**Nasazení databáze systému SQL Server na virtuálním počítači Microsoft Azure průvodce** je jednoduché a doporučený způsob, jak přesunout data z místní instance systému SQL Server do systému SQL Server na Virtuálním počítači Azure. Podrobný postup a také diskusi o další možnosti, najdete v části [migrace databáze na SQL Server na Virtuálním počítači Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportovat do plochých souborů
Různé metody slouží k hromadně exportovat data z místního SQL serveru, jak je uvedeno v [hromadný Import a Export dat (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) tématu. Tento dokument se týká Program hromadného kopírování (BCP) jako příklad. Jakmile se data se exportují do plochého souboru, můžete importovat na jiný SQL server pomocí hromadného importu.

1. Exportovat data z místního SQL serveru do souboru následujícím způsobem použití nástroje bcp

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Vytvoření databáze a tabulky na virtuální počítač s SQL serverem v Azure s využitím `create database` a `create table` pro schéma tabulky exportovali v kroku 1.
3. Vytvořte soubor formátu pro popis schématu tabulky se importovat/exportovat data. Podrobnosti o formátu souboru jsou popsány v [vytvořit soubor formátu (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formátování generování souboru při spuštění BCP z počítače systému SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Formátování generování souboru při spuštění BCP vzdáleně s SQL serverem

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Použít některou z metod popsaných v části [přesouvání dat ze zdrojového souboru](#filesource_to_sqlonazurevm) pro přesun dat do SQL serveru do plochých souborů.

### <a name="sql-migration"></a>Průvodce migrací služby SQL Database
[Průvodce migrací sady SQL Server Database](https://sqlazuremw.codeplex.com/) poskytuje uživatelsky přívětivé pro přesun dat mezi dvěma instancemi serveru SQL. Umožňuje uživateli mapování schématu dat mezi zdroji a cílové tabulky, zvolte typy sloupců a různé další funkce. Hromadné kopírování (BCP) na pozadí používá. Snímek obrazovky s úvodní obrazovce Průvodce migrací databáze SQL je uveden níže.  

![Průvodce migrací sady SQL Server][2]

### <a name="sql-backup"></a>Databáze back up a obnovení
Systém SQL Server podporuje:

1. [Databáze back up a obnovení funkce](https://msdn.microsoft.com/library/ms187048.aspx) (i k místnímu souboru nebo souboru bacpac exportovat do objektu blob) a [datové vrstvy aplikace](https://msdn.microsoft.com/library/ee210546.aspx) (souboru bacpac s použitím).
2. Schopnost vytvářet virtuální počítače s SQL serverem v Azure s zkopírovaný databází nebo zkopírovat do existující databázi SQL Azure. Další informace najdete v tématu [použití Průvodce kopírováním databáze](https://msdn.microsoft.com/library/ms188664.aspx).

Snímek databáze back up/obnovení možnosti systému SQL Server Management Studio najdete níž.

![Nástroj pro Import SQL serveru][1]

## <a name="resources"></a>Materiály
[Migrace databáze na SQL Server na Virtuálním počítači Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[SQL Server na Azure Virtual Machines – přehled](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
