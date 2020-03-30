---
title: 'Prostředí PowerShell: Migrace instance spravované sql serverem do SQL'
titleSuffix: Azure Database Migration Service
description: Naučte se migrovat z místní instance SQL Serveru do spravované instance Azure SQL Database pomocí Azure PowerShellu a služby Migrace databáze Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 9ea9f55681b93e79eec836f5808d2c6feaa6bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650720"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>Migrace SQL Serveru do spravované instance SQL Database pomocí powershellu & služby migrace databáze Azure

V tomto článku migrujete databázi **Adventureworks2016** obnovenou do místní instance SQL Serveru 2005 nebo vyšší ho do spravované instance Azure SQL Database pomocí Microsoft Azure PowerShellu. Databáze z místní instance SERVERU SQL Server můžete migrovat do spravované `Az.DataMigration` instance Azure SQL Database pomocí modulu v Prostředí Microsoft Azure PowerShell.

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
>
> * Vytvořte skupinu prostředků.
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace v instanci služby Migrace databáze Azure.
> * Spuštění migrace

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek obsahuje podrobnosti o tom, jak provádět migrace online i offline.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést tyto kroky, potřebujete:

* [SQL Server 2016 nebo vyšší](https://www.microsoft.com/sql-server/sql-server-downloads) (libovolné vydání).
* Místní kopie databáze **AdventureWorks2016,** která je k dispozici ke stažení [zde](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Povolení protokolu TCP/IP, který je ve výchozím nastavení zakázán instalací serveru SQL Server Express. Povolte protokol TCP/IP podle článku [Povolit nebo zakázat síťový protokol serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Konfigurace brány [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Předplatné Azure. Pokud ho nemáte, [vytvořte si účet zdarma,](https://azure.microsoft.com/free/) než začnete.
* Instance spravované azure SQL database. Spravovanou instanci Azure SQL Database můžete vytvořit podle podrobností v článku [Vytvoření spravované instance Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Stažení a instalace [Pomocníka pro migraci dat](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 nebo novějším.
* Virtuální síť Microsoft Azure vytvořená pomocí modelu nasazení Azure Resource Manageru, která poskytuje službě migrace databáze Azure připojení k místním zdrojovým serverům pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Dokončené vyhodnocení místní databáze a migrace schématu pomocí Pomocníka pro migraci dat, jak je popsáno v článku [Provádění vyhodnocení migrace serveru SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Chcete-li stáhnout `Az.DataMigration` a nainstalovat modul (verze 0.7.2 nebo novější) z Galerie prostředí PowerShell pomocí [rutiny Install-Module PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Chcete-li zajistit, že pověření použitá pro připojení ke zdrojové instanci serveru SQL Server mají oprávnění [ŘÍDICÍHO SERVERU.](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)
* Chcete-li zajistit, že přihlašovací údaje používané pro připojení k cílové instanci spravované azure SQL database má oprávnění databáze CONTROL DATABASE na cílové databáze spravovaných instancí Azure SQL Database.

    > [!IMPORTANT]
    > U migrace online už musíte mít nastavené přihlašovací údaje služby Azure Active Directory. Další informace naleznete v článku [Pomocí portálu vytvořte instanční objekt azure a služby, který má přístup k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Přihlášení k předplatnému Microsoft Azure

Přihlaste se ke svému předplatnému Azure pomocí PowerShellu. Další informace najdete v článku [Přihlášení pomocí Azure PowerShellu](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) pomocí příkazu.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *USA – východ.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Vytvoření instance služby Migrace databáze Azure

Novou instanci služby Migrace databáze `New-AzDataMigrationService` Azure můžete vytvořit pomocí rutiny.
Tato rutina očekává následující požadované parametry:

* *Název skupiny prostředků Azure*. Příkaz můžete [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) použít k vytvoření skupiny prostředků Azure, jak bylo dříve zobrazeno a zadat její název jako parametr.
* *Název služby*. Řetězec, který odpovídá požadovanému jedinečnému názvu služby pro službu Migrace databáze Azure.
* *Umístění*. Určuje umístění služby. Zadejte umístění datového centra Azure, například západní USA nebo jihovýchodní Asie.
* *Sku*. Tento parametr odpovídá názvu DMS Sku. Aktuálně podporované názvy sku jsou *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Identifikátor virtuální podsítě*. Rutinu můžete použít [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) k vytvoření podsítě.

Následující příklad vytvoří službu s názvem *MyDMS* ve skupině prostředků *MyDMSResourceGroup* umístěné v oblasti *USA – východ* pomocí virtuální sítě s názvem *MyVNET* a podsítě s názvem *MySubnet*.

> [!IMPORTANT]
> Fragment kódu níže je pro offline migraci, která nevyžaduje instanci služby Migrace databáze Azure na základě sku Premium. Pro online migraci musí hodnota parametru -Sku obsahovat prémiovou skladovou položku.

```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření instance služby migrace databáze Azure vytvořte projekt migrace. Projekt služby migrace databáze Azure vyžaduje informace o připojení pro zdrojové i cílové instance, stejně jako seznam databází, které chcete migrovat jako součást projektu.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Vytvoření objektu Informace o připojení k databázi pro zdrojová a cílová připojení

Objekt Informace o připojení k databázi můžete vytvořit pomocí `New-AzDmsConnInfo` rutiny, která očekává následující parametry:

* *ServerType*. Požadovaný typ připojení k databázi, například SQL, Oracle nebo MySQL. Použijte SQL pro SQL Server a Azure SQL.
* *Zdroj dat*. Název nebo IP instance serveru SQL Server nebo instance Azure SQL Database.
* *AuthType*. Typ ověřování pro připojení, který může být buď SqlAuthentication nebo WindowsAuthentication.
* *Důvěryhodný certifikát ServerCertificate*. Tento parametr nastaví hodnotu, která označuje, zda je kanál šifrován při vynechání chůze řetězu certifikátů k ověření důvěryhodnosti. Hodnota může `$true` být `$false`nebo .

Následující příklad vytvoří objekt Informace o připojení pro zdrojový SQL Server s názvem *MySourceSQLServer* pomocí ověřování SQL:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Další příklad ukazuje vytvoření informací o připojení pro server spravovaných instancí Azure SQL Database s názvem targetmanagedinstance.database.windows.net pomocí ověřování SQL:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Poskytnout databáze pro projekt migrace

Vytvořte seznam `AzDataMigrationDatabaseInfo` objektů, který určuje databáze jako součást projektu služby Migrace databáze Azure, který lze poskytnout jako parametr pro vytvoření projektu. Pomocí rutiny `New-AzDataMigrationDatabaseInfo` můžete vytvořit `AzDataMigrationDatabaseInfo`.

Následující příklad vytvoří `AzDataMigrationDatabaseInfo` projekt pro databázi **AdventureWorks2016** a přidá jej do seznamu, který má být poskytnut jako parametr pro vytvoření projektu.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Vytvoření objektu projektu

Nakonec můžete vytvořit projekt služby migrace databáze Azure s názvem *MyDMSProject* umístěný v *USA – východ* `New-AzDataMigrationProject` a přidat dříve vytvořená zdrojová a cílová připojení a seznam databází, které chcete migrovat.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Vytvoření a spuštění úlohy migrace

Dále vytvořte a spusťte úlohu služby migrace databáze Azure. Tato úloha vyžaduje informace o pověření připojení pro zdroj i cíl, stejně jako seznam databázových tabulek, které mají být migrovány a informace již poskytnuté s projektem vytvořené jako předpoklad.

### <a name="create-credential-parameters-for-source-and-target"></a>Vytvoření parametrů pověření pro zdroj a cíl

Vytvořte pověření zabezpečení připojení jako objekt [PSCredential.](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)

Následující příklad ukazuje vytvoření objektů *PSCredential* pro zdrojová i cílová připojení a poskytuje hesla jako proměnné řetězce *$sourcePassword* a *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Vytvoření záložního objektu FileShare

Teď vytvořte objekt FileShare představující místní sdílenou síťovou složku SMB, do které `New-AzDmsFileShare` může služba Azure Database Migration Service převzít zálohy zdrojové databáze pomocí rutiny.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Vytvoření vybraného databázového objektu

Dalším krokem je výběr zdrojové a cílové `New-AzDmsSelectedDB` databáze pomocí rutiny.

Následující příklad je pro migraci jedné databáze z SQL Server do spravované instance Azure SQL Database:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Pokud celá instance SQL Serveru potřebuje výtah a posun do spravované instance Azure SQL Database, pak smyčka převzít všechny databáze ze zdroje je k dispozici níže. V následujícím příkladu pro $Server, $SourceUserName a $SourcePassword zadejte podrobnosti zdrojového serveru SQL Server.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>Identifikátor URI Služby SAS pro kontejner úložiště Azure

Vytvořte proměnnou obsahující identifikátor URI SAS, který poskytuje službě Migrace databáze Azure přístup ke kontejneru účtu úložiště, do kterého služba nahrává záložní soubory.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Další požadavky na konfiguraci

Existuje několik dalších požadavků, které je třeba řešit, ale liší se v závislosti na tom, zda provádíte migraci offline nebo online.

#### <a name="offline-migrations"></a>Offline migrace

Pouze pro migrace offline proveďte následující další úlohy konfigurace.

* **Vyberte přihlášení**. Vytvořte seznam přihlášení, která mají být migrována, jak je znázorněno v následujícím příkladu:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > V současné době služba migrace databáze Azure podporuje jenom migrace přihlášení SQL.

* **Vyberte úlohy agenta**. Vytvořte seznam úloh agentů, které mají být migrovány, jak je znázorněno v následujícím příkladu:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > V současné době služba Migrace databáze Azure podporuje jenom úlohy s kroky úloh podsystému T-SQL.

#### <a name="online-migrations"></a>Migrace online

Pouze pro migrace online proveďte následující další úlohy konfigurace.

* **Konfigurace aplikace Azure Active Directory**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Konfigurace prostředku úložiště**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Vytvoření a spuštění úlohy migrace

Pomocí `New-AzDataMigrationTask` rutiny vytvořte a spusťte úlohu migrace.

#### <a name="specify-parameters"></a>Zadat parametry

##### <a name="common-parameters"></a>Běžné parametry

Bez ohledu na to, zda provádíte offline `New-AzDataMigrationTask` nebo online migraci, rutina očekává následující parametry:

* *Typ úkolu*. Typ úlohy migrace, která má být pro SQL Server do Azure SQL Database Spravované instance typu migrace *MigraceSqlServerSqlDbMi* se očekává. 
* *Název skupiny prostředků*. Název skupiny prostředků Azure, ve kterém chcete vytvořit úkol.
* *ServiceName*. Instance služby Migrace databáze Azure, ve které chcete úlohu vytvořit.
* *Název_projektu*. Název projektu služby Migrace databáze Azure, ve kterém chcete vytvořit úkol. 
* *Název_úkolu*. Název úkolu, který má být vytvořen. 
* *SourceConnection*. Objekt AzDmsConnInfo představující zdrojové připojení k serveru SQL Server.
* *TargetConnection*. Objekt AzDmsConnInfo představující cílové připojení spravované instance azure sql database.
* *SourceCred*. [Objekt PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) pro připojení ke zdrojovému serveru.
* *Cílcred*. [Objekt PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) pro připojení k cílovému serveru.
* *Vybraná databáze*. Objekt AzDataMigrationSelectedDB představující mapování zdrojové a cílové databáze.
* *BackupFileShare*. FileShare objekt představující místní síťovou sdílenou složku, do které může služba Migrace databáze Azure převést zálohy zdrojové databáze.
* *BackupBlobSasUri*. Identifikátor URI SAS, který poskytuje službě migrace databáze Azure přístup ke kontejneru účtu úložiště, do kterého služba nahrává záložní soubory. Zjistěte, jak získat identifikátor URI SAS pro kontejner objektů blob.
* *SelectedLogins*. Seznam vybraných přihlášení k migraci.
* *Vybrané úlohy agenta*. Seznam vybraných úloh agenta k migraci.

##### <a name="additional-parameters"></a>Další parametry

Rutina `New-AzDataMigrationTask` také očekává parametry, které jsou jedinečné pro typ migrace, offline nebo online, které provádíte.

* **Offline migrace**. Pro migrace offline `New-AzDataMigrationTask` rutina také očekává následující parametry:

  * *SelectedLogins*. Seznam vybraných přihlášení k migraci.
  * *Vybrané úlohy agenta*. Seznam vybraných úloh agenta k migraci.

* **Migrace online**. Pro online migrace `New-AzDataMigrationTask` rutina také očekává následující parametry.

* *AzureActiveDirectoryApp*. aplikace služby Active Directory.
* *StorageResourceID*. ID prostředku účtu úložiště.

#### <a name="create-and-start-an-offline-migration-task"></a>Vytvoření a zahájení úlohy offline migrace

Následující příklad vytvoří a spustí úlohu migrace offline s názvem **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Vytvoření a spuštění úlohy migrace online

Následující příklad vytvoří a spustí úlohu migrace online s názvem **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Monitorování migrace

Chcete-li migraci sledovat, proveďte následující úkoly.

1. Konsolidujte všechny podrobnosti migrace do proměnné s názvem $CheckTask.

    Chcete-li kombinovat podrobnosti migrace, jako jsou vlastnosti, stav a informace o databázi spojené s migrací, použijte následující fragment kódu:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Proměnnou `$CheckTask` můžete použít k získání aktuálního stavu úlohy migrace.

    Chcete-li `$CheckTask` použít proměnnou k získání aktuálního stavu úlohy migrace, můžete sledovat úlohu migrace spuštěnou dotazováním na vlastnost stavu úlohy, jak je znázorněno v následujícím příkladu:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Provedení přímého přechodu (pouze migrace online)

Při migraci online se provádí úplné zálohování a obnovení databází a potom práce pokračuje v obnovení transakční protokoly uložené v BackupFileShare.

Když je databáze ve spravované instanci Azure SQL Database aktualizována nejnovějšími daty a synchronizuje se se zdrojovou databází, můžete provést přímá hod.

Následující příklad dokončí přímá spoj/migrace. Uživatelé vyvolat tento příkaz podle svého uvážení.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Odstranění instance služby Migrace databáze Azure

Po dokončení migrace můžete odstranit instanci služby Migrace databáze Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Další zdroje

Informace o dalších scénářích migrace (dvojice zdroj/cíl) naleznete v [Příručce pro migraci do databáze](https://datamigration.microsoft.com/)společnosti Microsoft .

## <a name="next-steps"></a>Další kroky

Další informace o službě Migrace databáze Azure najdete v článku [Co je služba migrace databáze Azure?](https://docs.microsoft.com/azure/dms/dms-overview)
