---
title: Migrace SQL serveru do spravované Instance Azure SQL Database pomocí služby Database Migration Service a Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak migrovat z místního SQL serveru do Azure SQL DB Managed Instance pomocí Azure Powershellu.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/29/2019
ms.openlocfilehash: 96ee3f5e1b3cfe67cb75e50c6247e41f0d901393
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867924"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>Migrace místního SQL serveru do spravované instance Azure SQL Database pomocí prostředí Azure PowerShell
V tomto článku, migrujete **Adventureworks2016** databázi obnovit do místní instance systému SQL Server 2005 nebo nad ke službě Azure SQL Database managed instance pomocí prostředí Azure PowerShell. Můžete migrovat databáze z místní instance SQL serveru do spravované instance Azure SQL Database s použitím `Az.DataMigration` modulu v prostředí Azure PowerShell.

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
>
> * Vytvořte skupinu prostředků.
> * Vytvoření instance služby Azure Database Migration Service.
> * Vytvořte projekt migrace v instanci služby Azure Database Migration Service.
> * Spuštění migrace

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek obsahuje podrobnosti o tom, jak provádět online i offline migrace.

## <a name="prerequisites"></a>Požadavky

K dokončení těchto kroků budete potřebovat:

* [SQL Server 2016 nebo novější](https://www.microsoft.com/sql-server/sql-server-downloads) (libovolná edice).
* Místní kopii **AdventureWorks2016** databázi, která je k dispozici ke stažení [tady](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Chcete-li povolit protokol TCP/IP, který je zakázaný ve výchozím nastavení se instalaci systému SQL Server Express. Povolení protokolu TCP/IP pomocí následujícího článku [povolení nebo zakázání síťového protokolu serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Ke konfiguraci vaší [brány Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Azure SQL Database managed instance. Spravované instance Azure SQL Database můžete vytvořit pomocí následujících podrobností v článku [vytvoření spravované instance Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Ke stažení a instalaci [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 nebo novější.
* Azure Virtual Network (VNet) vytvořené pomocí modelu nasazení Azure Resource Manageru, které poskytuje Azure Database Migration Service pomocí připojení site-to-site k vašim serverům místní zdroj pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Dokončené posouzení místní databáze a schéma migraci pomocí Data Migration Assistant, jak je popsáno v článku [provádění posouzení migrace systému SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Stáhnout a nainstalovat `Az.DataMigration` modulu (verze 0.7.2 nebo novější) z Galerie prostředí PowerShell pomocí [rutinu Powershellu Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Chcete-li zajistit, aby měli pověření používaná k připojení ke zdrojové instanci SQL serveru [ovládacího PRVKU serveru](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) oprávnění.
* Chcete-li zajistit přihlašovací údaje použité pro připojení k cílovou službu Azure SQL Database managed instance má oprávnění CONTROL DATABASE na cílové databáze Azure SQL Database managed instance.

    > [!IMPORTANT]
    > Pro online migrace, musíte už mít nastavení přihlašovacích údajů Azure Active Directory. Další informace najdete v článku [použití portálu k vytvoření aplikace a instančního objektu, který má přístup k prostředkům Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Přihlaste se k předplatnému Microsoft Azure

Přihlaste se ke svému předplatnému Azure pomocí Powershellu. Další informace najdete v článku [Přihlaste se pomocí Azure Powershellu](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve které se nasazují a spravují prostředky.

Vytvořte skupinu prostředků s použitím [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) příkazu.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *USA – východ* oblasti.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Vytvoření instance služby Azure Database Migration Service

Můžete vytvořit novou instanci služby Azure Database Migration Service pomocí `New-AzDataMigrationService` rutiny.
Tato rutina očekává, že následující povinné parametry:

* *Název skupiny prostředků Azure*. Můžete použít [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) příkazu vytvořte skupinu prostředků Azure, jak bylo dříve uvedeno a zadejte jeho název jako parametr.
* *Název služby*. Řetězec, který odpovídá název požadovaného jedinečná služba Azure Database Migration Service.
* *Umístění*. Určuje umístění služby. Zadejte umístění centra služby Azure data, třeba západní USA nebo jihovýchodní Asie.
* *Sku*. Tento parametr odpovídá názvu DMS Sku. Názvy aktuálně podporované skladové položky jsou *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Identifikátor virtuální podsítě*. Můžete použít rutinu [ `New-AzVirtualNetworkSubnetConfig` ](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) vytvořit podsíť.

Následující příklad vytvoří službu s názvem *MyDMS* ve skupině prostředků *MyDMSResourceGroup* umístěné v *USA – východ* pomocí virtuální sítě s názvem oblasti *MyVNET* a podsíť s názvem *MySubnet*.

> [!IMPORTANT]
> Následující fragment kódu je pro offline migrace, která nevyžaduje instanci služby Azure Database Migration Service podle SKU úrovně Premium. Online migrace musí obsahovat hodnotu parametru - Sku SKU úrovně Premium.

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

Po vytvoření instance služby Azure Database Migration Service, vytvořte projekt migrace. Projekt Azure Database Migration Service vyžaduje informace o připojení pro obě instance zdroje a cíle, jakož i seznam databází, které chcete migrovat v rámci projektu.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Vytvoří objekt informace o připojení databáze pro zdroj a cíl připojení

Informace o připojení databáze objekt můžete vytvořit pomocí `New-AzDmsConnInfo` rutiny, která očekává, že následující parametry:

* *ServerType*. Typ připojení k databázi vyžádaný, například SQL, Oracle nebo MySQL. Použití SQL pro SQL Server a Azure SQL.
* *Zdroj dat*. Název nebo IP adresu instance systému SQL Server nebo instanci Azure SQL Database.
* *Typ AuthType*. Typ ověřování pro připojení, který může být SqlAuthentication nebo WindowsAuthentication.
* *TrustServerCertificate*. Tento parametr nastavuje hodnotu určující, zda kanál se šifrují při obcházení walking řetěz certifikátů pro ověření vztahu důvěryhodnosti. Hodnota může být `$true` nebo `$false`.

Následující příklad vytvoří objekt informace o připojení pro zdroj názvem serveru SQL Server *MySourceSQLServer* pomocí ověřování sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Následující příklad ukazuje vytvoření informace o připojení s názvem targetmanagedinstance.database.windows.net pomocí ověřování sql serveru spravované instance Azure SQL Database:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Zadejte projekt migrace databáze

Vytvoření seznamu `AzDataMigrationDatabaseInfo` objekty, které určuje projekt Azure Database Migration Service, který lze zadat jako parametr pro vytvoření projektu v rámci databází. Můžete použít rutinu `New-AzDataMigrationDatabaseInfo` vytvořit `AzDataMigrationDatabaseInfo`.

Následující příklad vytvoří `AzDataMigrationDatabaseInfo` projektu pro **AdventureWorks2016** databáze a přidá ji do seznamu zadat jako parametr pro vytvoření projektu.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Vytvořte objekt projektu

Nakonec můžete vytvořit projekt Azure Database Migration Service s názvem *MyDMSProject* umístěné v *USA – východ* pomocí `New-AzDataMigrationProject` a přidat dříve vytvořený zdroj a cíl připojení a seznam databází pro migraci.

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

V dalším kroku vytvořte a spusťte úlohu Azure Database Migration Service. Tato úloha vyžaduje připojení přihlašovací údaje pro zdroj a cíl, jak seznam tabulek databáze k migraci a informace, které jsou již součástí projektu vytvořeného jako předpoklad.

### <a name="create-credential-parameters-for-source-and-target"></a>Vytvoření parametry přihlašovací údaje pro zdroj a cíl

Vytvořte přihlašovací údaje zabezpečení připojení [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektu.

Následující příklad ukazuje vytvoření objektu *PSCredential* objekty pro zdroj a cíl připojení, poskytování hesla jako řetězec proměnné *$sourcePassword* a *$ Cílové_heslo*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Vytvořte objekt zálohování sdílenou složku.

Teď vytvořte sdílenou složku. objekt představující místní síťovou složku SMB, na který Azure Database Migration Service může umístit zdroj zálohy databáze pomocí `New-AzDmsFileShare` rutiny.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Vytvoření vybraný databázový objekt

Dalším krokem je vybrat zdrojovou a cílovou databázi pomocí `New-AzDmsSelectedDB` rutiny.

Následující příklad je určený pro izolované databáze migraci z SQL serveru do spravované instance Azure SQL Database:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Pokud musí celou instanci systému SQL Server lift and shift do služby Azure SQL Database managed instance, pak smyčky po uvedení všech databází ze zdroje jsou uvedeny níže. V následujícím příkladu $Server $SourceUserName a $SourcePassword, zadejte svůj zdroj podrobnosti SQL serveru.

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

### <a name="sas-uri-for-azure-storage-container"></a>Identifikátor URI SAS pro kontejner úložiště Azure

Vytvořte proměnnou obsahující identifikátor URI SAS, který poskytuje Azure Database Migration Service přístup ke kontejneru účtu úložiště, do které službu nahraje soubory zálohy.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Další konfigurační požadavky

Existuje několik dalších požadavků, které je potřeba vyhovět, ale liší v závislosti na tom, jestli při provádění migrace do režimu offline nebo online.

#### <a name="offline-migrations"></a>Offline migrace

Pouze v režimu offline migrace proveďte následující další konfigurační úlohy.

* **Vyberte přihlašovací jména**. Vytvoření seznamu přihlašovací jména k migraci, jak je znázorněno v následujícím příkladu:

    ```powershell
    $selectedLogins = @(“user1”, “user2”)
    ```

    > [!IMPORTANT]
    > Azure Database Migration Service v současné době podporuje jenom migrace přihlášeních SQL.

* **Vyberte úlohy agenta**. Vytvořte seznam, který agent úlohy k migraci, jak je znázorněno v následujícím příkladu:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Azure Database Migration Service v současné době podporuje pouze úlohy s kroky úlohy subsystému T-SQL.

#### <a name="online-migrations"></a>Online migrace

Pouze online migrace proveďte následující další konfigurační úlohy.

* **Konfigurace aplikace Azure Active Directory**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Konfigurace prostředků úložiště**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Vytvoření a spuštění úlohy migrace.

Použití `New-AzDataMigrationTask` rutina pro vytvoření a spuštění úlohy migrace.

#### <a name="specify-parameters"></a>Zadejte parametry

##### <a name="common-parameters"></a>Společné parametry

Bez ohledu na to, zda při provádění migrace do režimu offline nebo online `New-AzDataMigrationTask` rutina očekává následující parametry:

* *TaskType*. Typ úlohy migrace k vytvoření SQL serveru do Azure SQL Database Managed Instance, typ migrace *MigrateSqlServerSqlDbMi* očekává. 
* *Název skupiny prostředků*. Název skupiny prostředků Azure, ve kterém chcete vytvořit úlohu.
* *ServiceName*. Instance Azure Database Migration Service ve kterém chcete vytvořit úlohu.
* *ProjectName*. Název projektu Azure Database Migration Service, ve kterém chcete vytvořit úlohu. 
* *TaskName*. Název úkolu, který chcete vytvořit. 
* *SourceConnection*. AzDmsConnInfo objekt představující připojení ke zdroji systému SQL Server.
* *TargetConnection*. AzDmsConnInfo objekt představující cíl připojení Azure SQL Database Managed Instance.
* *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektu pro připojení ke zdrojovému serveru.
* *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektu pro připojení k cílovému serveru.
* *SelectedDatabase*. AzDataMigrationSelectedDB objekt reprezentující mapování zdrojové a cílové databázi.
* *BackupFileShare*. Sdílení souborů objekt reprezentující místní sdílené složce, která Azure Database Migration Service může umístit zdroj zálohy databáze.
* *BackupBlobSasUri*. Identifikátor URI SAS, který poskytuje Azure Database Migration Service přístup ke kontejneru účtu úložiště, do které službu nahraje soubory zálohy. Zjistěte, jak získat identifikátor URI SAS pro kontejner objektů blob.
* *SelectedLogins*. Seznam vybraných přihlášení k migraci.
* *SelectedAgentJobs*. Seznam vybraný agent úlohy pro migraci.

##### <a name="additional-parameters"></a>Další parametry

`New-AzDataMigrationTask` Rutina také očekává parametry, které jsou jedinečné pro typ migrací, offline nebo online, že provádíte.

* **Offline migrace**. Pro offline migrace `New-AzDataMigrationTask` rutina očekává také následující parametry:

  * *SelectedLogins*. Seznam vybraných přihlášení k migraci.
  * *SelectedAgentJobs*. Seznam vybraný agent úlohy pro migraci.

* **Online migrace**. Pro online migrace `New-AzDataMigrationTask` rutina očekává také následující parametry.

* *AzureActiveDirectoryApp*. Aplikace služby Active Directory.
* *StorageResourceID*. ID prostředku účtu úložiště.

#### <a name="create-and-start-an-offline-migration-task"></a>Vytvořte a spusťte úlohu offline migrace

Následující příklad vytvoří a spustí úlohu offline migrace s názvem **myDMSTask**:

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

#### <a name="create-and-start-an-online-migration-task"></a>Vytvořte a spusťte úlohu online migrace

Následující příklad vytvoří a spustí úlohu online migrace s názvem **myDMSTask**:

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

Monitorování migrace, proveďte následující úlohy.

1. Konsolidovat všechny podrobnosti o migraci do proměnnou s názvem $CheckTask.

    Kombinování podrobnosti o migraci, jako jsou vlastnosti, stav a související s migrací informací o databázi, použijte následující fragment kódu:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Použití `$CheckTask` proměnné se získat aktuální stav úlohy migrace.

    Použít `$CheckTask` proměnné získat aktuální stav úlohy migrace, můžete monitorovat úlohy migrace s dotazem na vlastnost stavu úkolu, jak je znázorněno v následujícím příkladu:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      write-host "migration task running"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      write-host "Migration task is completed Successfully"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation"  -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      write-host “Migration Task Failed”
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Provádění přímou migraci (online migrace pouze)

Online migraci je provést úplnou zálohu a obnovu databáze a pak pokračuje práce na obnově uložené v BackupFileShare protokolů transakcí.

Pokud databáze ve spravované instanci Azure SQL Database je aktualizován nejnovějšími daty a je synchronizovaný s zdrojové databáze, můžete provést přímou migraci.

V následujícím příkladu se dokončí cutover\migration. Uživatelé vyvolání tohoto příkazu na prodejci podle vlastního uvážení.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Odstranění instance služby Azure Database Migration Service

Po dokončení migrace můžete odstranit instanci Azure Database Migration Service:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Další materiály

Informace o dalších scénářů migrace (párů zdroj/cíl), najdete v článku Microsoft [Průvodce migrací databází](https://datamigration.microsoft.com/).

## <a name="next-steps"></a>Další postup

Další informace o Azure Database Migration Service najdete v článku [co je Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
