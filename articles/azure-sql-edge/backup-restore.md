---
title: Zálohování a obnovení databází – Azure SQL Edge
description: Přečtěte si o možnostech zálohování a obnovení v Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 114be810ea50f984c3211291691b4c4dd45ac2c7
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395236"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge"></a>Zálohování a obnovení databází v Azure SQL Edge 

Azure SQL Edge je založený na nejnovějších verzích modulu Microsoft SQL Database Engine. Nabízí podobné možnosti zálohování a obnovení databáze jako dostupné v SQL Server on Linux a SQL Server spouštění v kontejnerech. Součást zálohování a obnovení poskytuje základní ochranu pro ochranu dat uložených v databázích Azure SQL Edge. 

K minimalizaci rizika závažné ztráty dat byste měli zálohovat databáze pravidelně, abyste mohli pravidelně uchovávat změny vašich dat. Dobře naplánovaná strategie zálohování a obnovení pomáhá chránit databáze před ztrátou dat způsobenou nejrůznějšími selháními. Otestujte svoji strategii obnovením sady záloh a následnou obnovením databáze, abyste se připravili na případnou reakci.

Další informace o tom, proč jsou zálohy důležité, najdete v tématu [zálohování a obnovení databází SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

Azure SQL Edge vám umožní zálohovat a obnovovat z místního úložiště i z objektů BLOB v Azure. Další informace najdete v tématu [SQL Server zálohování a obnovování pomocí služby Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) a [SQL Server zálohování na adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="back-up-a-database-in-azure-sql-edge"></a>Zálohování databáze ve službě Azure SQL Edge

Azure SQL Edge podporuje stejné typy zálohování jako SQL Server. Úplný seznam najdete v tématu [Přehled zálohování](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Databáze vytvořené ve službě Azure SQL Edge používají ve výchozím nastavení jednoduchý model obnovení. V takových databázích proto nemůžete provádět zálohy protokolů. Pokud to potřebujete udělat, budete potřebovat správce, aby změnil model obnovení databáze na model úplného obnovení. Úplný seznam modelů obnovení podporovaných nástrojem SQL Server najdete v tématu [Přehled modelu obnovení](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="back-up-to-local-disk"></a>Zálohování na místní disk

V následujícím příkladu použijete `BACKUP DATABASE` příkaz Transact-SQL k vytvoření zálohy databáze v kontejneru. Pro účely tohoto příkladu vytvoříte novou složku s názvem *Backup* pro uložení záložních souborů.

1. Vytvořte složku pro zálohy. Spusťte tento příkaz na hostiteli, kde je spuštěný váš kontejner Azure SQL Edge. V následujícím příkazu nahraďte **<AzureSQLEdge_Container_Name>** názvem kontejneru Azure SQL Edge v nasazení.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Připojte se k instanci Azure SQL Edge pomocí SQL Server Management Studio (SSMS) nebo pomocí Azure Data Studio. Spusťte `BACKUP DATABASE` příkaz, který provede zálohování vaší uživatelské databáze. V následujícím příkladu přenášíte zálohu databáze *IronOreSilicaPrediction* .

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Pokud se po spuštění příkazu spustí zálohování databáze, zobrazí se v části výsledky v SSMS nebo Azure Data Studio zprávy podobné následujícímu.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="back-up-to-url"></a>Zálohovat na adresu URL

Azure SQL Edge podporuje zálohování do objektů blob stránky a objektů blob bloku. Další informace najdete v tématu [zálohování objektů blob bloku](/sql/relational-databases/backup-restore/sql-server-backup-to-url#blockbloborpageblob)objektů blob stránky. V následujícím příkladu se databáze *IronOreSilicaPrediction* zálohuje do objektu blob bloku. 

1. Pokud chcete nakonfigurovat zálohování pro blokování objektů blob, nejdřív vygenerujte token sdíleného přístupového podpisu (SAS), který můžete použít k vytvoření SQL Server přihlašovacích údajů na Edge SQL Azure. Skript vytvoří SAS, která je přidružená k zásadám uloženého přístupu. Další informace najdete v tématu [signatury sdíleného přístupu, část 1: Princip modelu SAS](../storage/common/storage-sas-overview.md). Skript také zapíše příkaz T-SQL vyžadovaný k vytvoření přihlašovacích údajů na SQL Server. Následující skript předpokládá, že už máte předplatné Azure s účtem úložiště a kontejner úložiště pro zálohy.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    Po úspěšném spuštění skriptu zkopírujte `CREATE CREDENTIAL` příkaz do nástroje pro dotazování. Pak se připojte k instanci SQL Server a spusťte příkaz pro vytvoření přihlašovacích údajů s SAS.

2. Připojte se k instanci Azure SQL Edge pomocí SSMS nebo Azure Data Studio a vytvořte přihlašovací údaje pomocí příkazu z předchozího kroku. Ujistěte se, že jste `CREATE CREDENTIAL` příkaz nahradili skutečným výstupem z předchozího kroku.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. Následující příkaz vytvoří zálohu *IronOreSilicaPrediction* do kontejneru úložiště Azure.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restore-a-database-in-azure-sql-edge"></a>Obnovení databáze ve službě Azure SQL Edge

V Azure SQL Edge můžete obnovení obnovit z místního disku, umístění v síti nebo účtu úložiště objektů BLOB v Azure. Další informace o obnovení a obnovení v SQL Server najdete v tématu [Přehled obnovení a obnovení](/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server). Přehled jednoduchého modelu obnovení v SQL Server najdete v tématu úplné obnovení [databáze (jednoduchý model obnovení)](/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model).

> [!IMPORTANT] 
> Databáze vytvořené v Azure SQL Edge nejde obnovit na instanci Microsoft SQL Server nebo Azure SQL. Kromě toho může být databáze vytvořená v Microsoft SQL Server nebo Azure SQL obnovená na Edge SQL Azure, pokud databáze neobsahuje žádnou z funkcí, které Azure SQL Edge nepodporuje. 

### <a name="restore-from-a-local-disk"></a>Obnovení z místního disku

V tomto příkladu se používá záloha *IronOreSilicaPrediction* , kterou jste provedli v předchozím příkladu. Nyní ji obnovíte jako novou databázi s jiným názvem.

1. Pokud záložní soubor databáze již v kontejneru existuje, můžete k zkopírování souboru do kontejneru použít následující příkaz. V následujícím příkladu se předpokládá, že se záložní soubor nachází na místním hostiteli a kopíruje se do složky/var/opt/MSSQL/Backup do kontejneru Azure SQL Edge s názvem *SQL1*.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Připojte se k instanci Azure SQL Edge pomocí SSMS nebo Azure Data Studio spusťte příkaz Restore. V následujícím příkladu je **IronOrePredictDB. bak** obnovena pro vytvoření nové databáze **IronOreSilicaPrediction_2**.

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Po spuštění příkazu RESTORE se v případě úspěchu operace obnovení zobrazí v okně výstup zprávy podobné následujícímu. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Obnovit z adresy URL

Azure SQL Edge podporuje také obnovení databáze z účtu Azure Storage. Můžete obnovit buď z objektů blob bloku, nebo ze zálohy objektů blob stránky. V následujícím příkladu je obnoven záložní soubor databáze *IronOreSilicaPrediction_2020_04_16. bak* v objektu blob bloku, aby se vytvořila databáze, *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```