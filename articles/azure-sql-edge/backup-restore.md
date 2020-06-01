---
title: Zálohování a obnovení databází – Azure SQL Edge (Preview)
description: Další informace o možnostech zálohování a obnovení ve službě Azure SQL Edge (Preview)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 902576f82faa18fbb0e7c7eaed5c06993bd379cc
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235190"
---
# <a name="backup-and-restore-databases-in-azure-sql-edge-preview"></a>Zálohování a obnovení databází v Azure SQL Edge (Preview) 

Azure SQL Edge je založený na nejnovějších verzích Microsoft SQL Server databázového stroje v systému Linux a poskytuje podobné možnosti zálohování a obnovení databáze jako ty, které jsou k dispozici v SQL Server on Linux a SQL Server spuštěné v kontejnerech. Součást Backup a Restore poskytuje základní ochranu pro ochranu dat uložených v databázích Azure SQL Edge. Aby se minimalizovalo riziko závažné ztráty dat, doporučuje se pravidelně zálohovat databáze, abyste zachovali změny dat v pravidelných intervalech. Dobře naplánovaná strategie zálohování a obnovení pomáhá chránit databáze před ztrátou dat způsobenou nejrůznějšími selháními. Otestujte svou strategii obnovením sady záloh a následným obnovením databáze, abyste byli připraveni efektivně reagovat na havárie.

Další informace o tom, proč jsou zálohy důležité, najdete v tématu [zálohování a obnovení databází SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

Azure SQL Edge podporuje zálohování a obnovování z místního úložiště i z objektů BLOB v Azure. Další informace o zálohování a obnovení z Azure Blob Storage najdete v [SQL Server zálohování a obnovení pomocí služby Microsoft Azure Blob Storage Service](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) a [SQL Server zálohování na adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="backing-up-a-database-in-azure-sql-edge"></a>Zálohování databáze ve službě Azure SQL Edge

Azure SQL Edge podporuje stejné typy zálohování jako podporované SQL Server. Úplný seznam typů zálohování podporovaných v SQL Server najdete v tématu [Přehled zálohování](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Databáze vytvořené ve službě Azure SQL Edge používají ve výchozím nastavení jednoduchý model obnovení. Protože takové zálohy protokolu nelze na těchto databázích provádět. Pokud je nutné provést zálohování protokolů v těchto databázích, správce bude muset změnit model obnovení databáze na úplný model obnovení. Úplný seznam modelů obnovení podporovaných nástrojem SQL Server najdete v tématu [Přehled modelu obnovení](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="backup-to-local-disk"></a>Zálohovat na místní disk

V níže uvedeném příkladu se k vytvoření zálohy databáze v kontejneru používá příkaz Transact-SQL BACKUP DATABASE. Pro účely tohoto příkladu je pro uložení záložních souborů vytvořena nová složka s názvem "zálohování".

1. Vytvořte složku pro zálohy. Tento příkaz se musí provést na hostiteli, kde je spuštěný váš kontejner Azure SQL Edge. V následujícím příkazu nahraďte **<AzureSQLEdge_Container_Name>** názvem kontejneru Azure SQL Edge v nasazení.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Připojte se k instanci Azure SQL Edge pomocí SQL Server Management Studio (SSMS) nebo pomocí Azure Data Studio (ADS) a spusťte příkaz Backup Database a proveďte zálohu uživatelské databáze. V následujícím příkladu přebíráme zálohu databáze *IronOreSilicaPrediction* .

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Po spuštění příkazu a pokud je zálohování databáze úspěšné, zobrazí se v části výsledky v SSMS nebo reklam zpráva podobná následující.

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

### <a name="backup-to-url"></a>Záloha na adresu URL

Azure SQL Edge podporuje zálohování do objektů blob stránky a objektů blob bloku. Další informace o objektech blob stránky a objektech blob bloku najdete v tématu [zálohování pro blokování objektů BLOB objektu a objektů blob stránky](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob). V následujícím příkladu se databáze *IronOreSilicaPrediction* zálohuje do objektu blob bloku. 

1. Prvním krokem při konfiguraci záloh pro blokování objektů BLOB je vygenerování tokenu SAS (Shared Access Signature), který se dá použít k vytvoření SQL Server přihlašovacích údajů na Edge SQL Azure. Skript vytvoří sdílený přístupový podpis, který je přidružený k uložené zásadě přístupu. Další informace najdete v tématu [signatury sdíleného přístupu, část 1: Princip modelu SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/). Skript také zapíše příkaz T-SQL vyžadovaný k vytvoření přihlašovacích údajů na SQL Server. Skript níže předpokládá, že už máte předplatné Azure s účtem úložiště a kontejnerem úložiště pro zálohy.

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

    Po úspěšném spuštění skriptu zkopírujte příkaz vytvořit pověření do nástroje pro dotaz, připojte se k instanci SQL Server a spusťte příkaz pro vytvoření přihlašovacích údajů se sdíleným přístupovým podpisem.

2. Připojte se k instanci Azure SQL Edge pomocí SQL Server Management Studio (SSMS) nebo pomocí Azure Data Studio (ADS) a vytvořte přihlašovací údaje pomocí příkazu z předchozího kroku. Nezapomeňte nahradit příkaz vytvořit pověření skutečným výstupem z předchozího kroku.

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

## <a name="restoring-a-database-in-azure-sql-edge"></a>Obnovení databáze ve službě Azure SQL Edge

Azure SQL Edge podporuje obnovení z místního disku, síťového umístění nebo účtu Azure Blob Storage. Přehled obnovení a obnovení v SQL Server najdete v tématu [Přehled obnovení a obnovení](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15). Přehled jednoduchého modelu obnovení v SQL Server najdete v tématu úplné obnovení [databáze (jednoduchý model obnovení)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15).

### <a name="restore-from-local-disk"></a>Obnovit z místního disku

V tomto příkladu se používá zálohování *IronOreSilicaPrediction* provedené v předchozím příkladu pro obnovení jako nová databáze s jiným názvem.

1. Pokud se záložní soubor databáze v kontejneru ještě nenachází, můžete k zkopírování souboru do kontejneru použít následující příkaz. Následující příklad předpokládá, že se záložní soubor nachází na místním hostiteli a kopíruje se do složky/var/opt/MSSQL/Backup do kontejneru Azure SQL Edge s názvem SQL1.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Připojte se k instanci Azure SQL Edge pomocí SQL Server Management Studio (SSMS) nebo pomocí Azure Data Studio (ADS) spusťte příkaz Restore. V následujícím příkladu je **IronOrePredictDB. bak** obnoven, aby se vytvořila nová databáze **IronOreSilicaPrediction_2**

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Po spuštění příkazu RESTORE a úspěšném dokončení operace obnovení se zobrazí zprávy podobné následujícímu v okně výstup. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Obnovit z adresy URL

Azure SQL Edge podporuje také obnovení databáze z účtu Azure Storage. Obnovení je možné provést buď z objektů blob bloku, nebo ze zálohy objektů blob stránky. V níže uvedeném příkladu je obnoven záložní soubor databáze *IronOreSilicaPrediction_2020_04_16. bak* v objektu blob bloku, aby se vytvořila *IronOreSilicaPrediction_3*databáze.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


