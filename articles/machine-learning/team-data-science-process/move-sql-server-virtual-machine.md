---
title: Přesunutí dat do virtuálního počítače sql serveru – proces vědecké analýzy týmových dat
description: Přesuňte data z plochých souborů nebo z místního SQL Serveru na SQL Server ve virtuálním počítači Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721366"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Přesun dat do SQL Serveru na virtuálním počítači Azure

Tento článek popisuje možnosti pro přesun dat z plochých souborů (formáty CSV nebo TSV) nebo z místního SQL Serveru na SQL Server na virtuálním počítači Azure. Tyto úkoly pro přesun dat do cloudu jsou součástí procesu vědecké ho sloužiny týmových dat.

Téma, které popisuje možnosti pro přesun dat do Azure SQL Database for Machine Learning, najdete v [tématu Přesun dat do Azure SQL Database for Azure Machine Learning](move-sql-azure.md).

Následující tabulka shrnuje možnosti pro přesun dat na SQL Server na virtuálním počítači Azure.

| <b>Zdroj</b> | <b>CÍL: SQL Server na virtuálním počítači Azure</b> |
| --- | --- |
| <b>Plochý soubor</b> |1. <a href="#insert-tables-bcp">Nástroj pro hromadné kopírování příkazového řádku (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">Hromadné vložení dotazu SQL</a><br> 3. <a href="#sql-builtin-utilities">Grafické vestavěné nástroje na SQL Serveru</a> |
| <b>Místní SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Nasazení databáze serveru SQL Server do průvodce virtuálním počítačem Microsoft Azure</a><br> 2. <a href="#export-flat-file">Export do plochého souboru</a><br> 3. <a href="#sql-migration">Průvodce migrací databáze SQL</a> <br> 4. <a href="#sql-backup">Zálohování a obnovení databáze</a><br> |

Tento dokument předpokládá, že příkazy SQL jsou spouštěny z SQL Server Management Studio nebo Visual Studio Database Explorer.

> [!TIP]
> Jako alternativu můžete pomocí [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) vytvořit a naplánovat kanál, který přesune data do virtuálního počítače SQL Server v Azure. Další informace najdete [v tématu Kopírování dat pomocí Azure Data Factory (Aktivita kopírování)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Požadavky
Tento kurz předpokládá, že máte:

* Předplatné **Azure**. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účet úložiště Azure**. Budete používat účet úložiště Azure pro ukládání dat v tomto kurzu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Po vytvoření účtu úložiště budete muset získat klíč účtu používaný pro přístup k úložišti. Viz [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).
* Zřízený **SQL Server na virtuálním počítači Azure**. Pokyny najdete [v tématu Nastavení virtuálního počítače Azure SQL Server jako notebooku IPython pro pokročilou analýzu](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Nainstalované a nakonfigurované **Azure PowerShell** místně. Pokyny najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a>Přesunutí dat ze zdroje plochých souborů na SQL Server na virtuálním počítači Azure
Pokud jsou vaše data v plochém souboru (uspořádaném ve formátu řádku/sloupce), můžete je přesunout do virtuálního počítače SQL Server v Azure pomocí následujících metod:

1. [Nástroj pro hromadné kopírování příkazového řádku (BCP)](#insert-tables-bcp)
2. [Hromadný vložit dotaz SQL](#insert-tables-bulkquery)
3. [Grafické vestavěné nástroje na SQL Serveru (import/export, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Nástroj pro hromadné kopírování příkazového řádku (BCP)
BCP je nástroj příkazového řádku nainstalovaný se serverem SQL Server a je jedním z nejrychlejších způsobů přesunu dat. Funguje napříč všemi třemi variantami SQL Serveru (místní SQL Server, SQL Azure a SQL Server VM v Azure).

> [!NOTE]
> **Kde by měly být moje údaje pro BCP?**  
> I když to není nutné, mít soubory obsahující zdrojová data umístěná ve stejném počítači jako cílový SQL Server umožňuje rychlejší přenosy (rychlost sítě vs rychlost vs. místní disk VO). Ploché soubory obsahující data můžete přesunout do počítače, ve kterém je nainstalován SQL Server, pomocí různých nástrojů pro kopírování souborů, jako je [AZCopy](../../storage/common/storage-use-azcopy.md), [Průzkumník úložiště Azure](https://storageexplorer.com/) nebo kopírování a vkládání oken prostřednictvím protokolu RDP (RdProtocol).
>
>

1. Ujistěte se, že databáze a tabulky jsou vytvořeny v cílové databázi serveru SQL Server. Zde je příklad, jak to `Create Database` udělat `Create Table` pomocí příkazů a:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Vygenerujte formátovací soubor, který popisuje schéma tabulky, vydáním následujícího příkazu z příkazového řádku počítače, ve kterém je nainstalována funkce bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Vložte data do databáze pomocí příkazu bcp, který by měl fungovat z příkazového řádku při instalaci serveru SQL Server ve stejném počítači:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Optimalizace vložek BCP** Pro optimalizaci těchto břitových destiček naleznete v následujícím článku ["Pokyny pro optimalizaci hromadného](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) importu".
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Paralela břitových destiček pro rychlejší přesun dat
Pokud jsou data, která přesouváte, velká, můžete věci urychlit současným spuštěním více příkazů BCP paralelně ve skriptu prostředí PowerShell.

> [!NOTE]
> **Požití velkých objemů dat** Chcete-li optimalizovat načítání dat pro velké a velmi velké datové sady, rozdělte logické a fyzické databázové tabulky pomocí více skupin souborů a tabulek oddílů. Další informace o vytváření a načítání dat do tabulek oddílů naleznete v [tématu Parallel Load SQL Partition Tables](parallel-load-sql-partitioned-tables.md).
>
>

Následující ukázkový skript prostředí PowerShell ukazuje paralelní vložení pomocí bcp:

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

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>Hromadný vložit dotaz SQL
[Hromadný vložit SQL Query](https://msdn.microsoft.com/library/ms188365) lze použít k importu dat do databáze z řádku/ sloupce založené soubory (podporované typy jsou zahrnuty v[připravit data pro hromadný export nebo import (SQL Server)](https://msdn.microsoft.com/library/ms188609)) téma.

Zde jsou některé ukázkové příkazy pro hromadné vkládání jsou následující:  

1. Analyzujte data a nastavte všechny vlastní možnosti před importem a ujistěte se, že databáze serveru SQL Server předpokládá stejný formát pro všechna zvláštní pole, jako jsou data. Zde je příklad, jak nastavit formát data jako rok-měsíc-den (pokud vaše data obsahují datum ve formátu rok-měsíc-den):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Import dat pomocí příkazů hromadného importu:

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

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Integrované nástroje na serveru SQL Server
K importu dat do virtuálního počítače SQL Serveru v Azure z plochého souboru můžete použít službu SQL Server Integration Services (SSIS).
SSIS je k dispozici ve dvou studiových prostředích. Podrobnosti naleznete [v tématu Integration Services (SSIS) a Studio Environments](https://technet.microsoft.com/library/ms140028.aspx):

* Podrobnosti o nástrojích SQL Server Data Tools naleznete v [tématu Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Podrobnosti o Průvodci importem a exportem naleznete v [průvodci importem a exportem serveru SQL Server.](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Přesunutí dat z místního SQL Serveru na SQL Server na virtuálním počítači Azure
Můžete také použít následující strategie migrace:

1. [Nasazení databáze serveru SQL Server do průvodce virtuálním počítačem Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportovat do plochého souboru](#export-flat-file)
3. [Průvodce migrací databáze SQL](#sql-migration)
4. [Zálohování a obnovení databáze](#sql-backup)

Každou z těchto možností popisujeme níže:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Nasazení databáze serveru SQL Server do průvodce virtuálním počítačem Microsoft Azure
Nasazení **databáze SQL Serveru do průvodce microsoft azure virtuálnípočítač** je jednoduchý a doporučený způsob, jak přesunout data z místní instance SQL Serveru na SQL Server na virtuálním počítači Azure. Podrobné kroky a diskusi o dalších alternativách najdete [v tématu Migrace databáze na SQL Server na virtuálním počítači Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportovat do plochého souboru
Různé metody lze použít k hromadnému exportu dat z místního serveru SQL Server, jak je zdokumentováno v tématu [Hromadné import a export dat (SQL Server).](https://msdn.microsoft.com/library/ms175937.aspx) Tento dokument bude zahrnovat program hromadného kopírování (BCP) jako příklad. Jakmile jsou data exportována do plochého souboru, lze je importovat na jiný server SQL pomocí hromadného importu.

1. Export dat z místního serveru SQL Server do souboru pomocí nástroje bcp následujícím způsobem

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Vytvořte databázi a tabulku na virtuálním `create database` počítači SQL Server v Azure pomocí `create table` schématu tabulky exportovaného v kroku 1.
3. Vytvořte soubor formátu pro popis schématu tabulky exportovaných nebo importovaných dat. Podrobnosti o souboru formátu jsou popsány v [části Vytvoření souboru formátu (SQL Server).](https://msdn.microsoft.com/library/ms191516.aspx)

    Formátování generování souborů při spuštění protokolu BCP z počítače se serverem SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Formátování generování souborů při vzdáleném spuštění protokolu BCP proti serveru SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Pomocí některé z metod popsaných v části [Přesunutí dat ze zdroje souboru](#filesource_to_sqlonazurevm) přesuňte data v plochých souborech na SQL Server.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>Průvodce migrací databáze SQL
[Průvodce migrací databáze serveru SQL Server](https://sqlazuremw.codeplex.com/) poskytuje uživatelsky přívětivý způsob přesouvání dat mezi dvěma instancemi serveru SQL. Umožňuje uživateli mapovat schéma dat mezi zdroji a cílovými tabulkami, zvolit typy sloupců a různé další funkce. Používá hromadné kopírování (BCP) pod kryty. Snímek obrazovky s uvítací obrazovkou průvodce migrací databáze SQL je uveden níže.  

![Průvodce migrací serveru SQL Server][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Zálohování a obnovení databáze
SQL Server podporuje:

1. [Databáze zálohovat a obnovit funkce](https://msdn.microsoft.com/library/ms187048.aspx) (do místního souboru nebo bacpac export do objektu blob) a [aplikace datové vrstvy](https://msdn.microsoft.com/library/ee210546.aspx) (pomocí bacpac).
2. Možnost přímo vytvářet virtuální počítače SQL Serveru v Azure pomocí zkopírované databáze nebo zkopírovat do existující databáze SQL Azure. Další informace naleznete [v tématu Use the Copy Database Wizard](https://msdn.microsoft.com/library/ms188664.aspx).

Snímek obrazovky databáze zálohovat nebo obnovit možnosti z SQL Server Management Studio je uvedeno níže.

![Nástroj pro import serveru SQL Server][1]

## <a name="resources"></a>Prostředky
[Migrace databáze na SQL Server na virtuálním počítači Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[SQL Server na Azure Virtual Machines – přehled](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
