---
title: 'PowerShell: migrace SQL Server do spravované instance SQL offline'
titleSuffix: Azure Database Migration Service
description: Přečtěte si, jak offline migrovat z SQL Server do spravované instance Azure SQL pomocí Azure PowerShell a Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 90663b6beb4f1e3f7ade32e603a53c8b9d9158f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697810"
---
# <a name="migrate-sql-server-to-sql-managed-instance-offline-with-powershell--azure-database-migration-service"></a>Migrace SQL Server do spravované instance SQL offline pomocí prostředí PowerShell & Azure Database Migration Service

V tomto článku jste offline migraci databáze **Adventureworks2016** obnovili do místní instance SQL Server 2005 nebo vyšší do spravované instance Azure SQL sql pomocí Microsoft Azure PowerShell. Databáze můžete migrovat z SQL Server instance do spravované instance SQL pomocí `Az.DataMigration` modulu v Microsoft Azure PowerShell.

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
>
> * Vytvořte skupinu prostředků.
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace v instanci Azure Database Migration Service.
> * Spusťte migraci offline.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje kroky pro offline migraci, ale je také možné migrovat [online](howto-sql-server-to-azure-sql-managed-instance-powershell-online.md).


## <a name="prerequisites"></a>Požadavky

K provedení těchto kroků potřebujete:

* [SQL Server 2016 nebo vyšší](https://www.microsoft.com/sql-server/sql-server-downloads) (jakákoli edice).
* Místní kopii databáze **AdventureWorks2016** , která je k dispozici ke stažení [zde](/sql/samples/adventureworks-install-configure).
* Povolení protokolu TCP/IP, který je ve výchozím nastavení zakázán při instalaci SQL Server Express. Povolte protokol TCP/IP podle článku [Povolení nebo zakázání síťového protokolu serveru](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Ke konfiguraci [brány Windows Firewall pro přístup k databázovému stroji](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Spravovaná instance SQL Pomocí podrobností v článku [Vytvoření spravované instance ASQL](../azure-sql/managed-instance/instance-create-quickstart.md)můžete vytvořit SPRAVOVANOU instanci SQL.
* Stažení a instalace [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 nebo novějších verzích.
* Microsoft Azure Virtual Network vytvořili pomocí modelu nasazení Azure Resource Manager, který poskytuje Azure Database Migration Service připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Dokončili jste vyhodnocení místní migrace databáze a schématu pomocí Data Migration Assistant, jak je popsáno v článku, který [provádí hodnocení migrace SQL Server](/sql/dma/dma-assesssqlonprem).
* Pokud chcete stáhnout a nainstalovat `Az.DataMigration` modul (verze 0.7.2 nebo novější) z Galerie prostředí PowerShell pomocí [rutiny Install-Module prostředí PowerShell](/powershell/module/powershellget/Install-Module).
* Aby bylo zajištěno, že přihlašovací údaje použité pro připojení ke zdrojové SQL Server instance mají oprávnění [Control Server](/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* Aby bylo zajištěno, že přihlašovací údaje použité pro připojení k cílové spravované instanci SQL mají oprávnění řídicí databáze pro cílové databáze spravované instance SQL.


## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Přihlaste se k předplatnému Microsoft Azure.

Přihlaste se ke svému předplatnému Azure pomocí PowerShellu. Další informace najdete v článku [přihlášení pomocí Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) příkazu.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *východní USA* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Vytvoření instance služby Azure Database Migration Service

Novou instanci Azure Database Migration Service můžete vytvořit pomocí `New-AzDataMigrationService` rutiny.
Tato rutina očekává následující požadované parametry:

* *Název skupiny prostředků Azure*. Pomocí příkazu můžete [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) vytvořit skupinu prostředků Azure, jak je uvedeno výše, a zadat její název jako parametr.
* *Název služby* Řetězec, který odpovídá požadovanému jedinečnému názvu služby pro Azure Database Migration Service.
* *Umístění:* Určuje umístění služby. Zadejte umístění datového centra Azure, například Západní USA nebo jihovýchodní Asie.
* *SKU*. Tento parametr odpovídá názvu SKU DMS. V současné době jsou podporovány názvy SKU *Basic_1vCore*, *Basic_2vCores* *GeneralPurpose_4vCores*.
* *Identifikátor virtuální podsítě*. Pomocí rutiny můžete [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) vytvořit podsíť.

Následující příklad vytvoří službu s názvem *MyDMS* ve skupině prostředků *MyDMSResourceGroup* nacházející se v *východní USA* oblasti pomocí virtuální sítě s názvem *MyVNET* a podsítě s názvem *MySubnet*.

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

Po vytvoření instance Azure Database Migration Service vytvořte projekt migrace. Azure Database Migration Service projekt vyžaduje informace o připojení ke zdrojové i cílové instanci i seznam databází, které chcete migrovat v rámci projektu.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Vytvoření objektu s informacemi o připojení databáze pro zdrojovou a cílovou připojení

Objekt s informacemi o připojení databáze můžete vytvořit pomocí `New-AzDmsConnInfo` rutiny, která očekává následující parametry:

* *ServerType*. Typ požadovaného databázového připojení, například SQL, Oracle nebo MySQL. Použijte SQL pro SQL Server a Azure SQL.
* *Zdroj dat* Název nebo IP adresa instance SQL Server nebo instance Azure SQL Database
* *Typ authType*. Typ ověřování pro připojení, který může být buď SqlAuthentication, nebo WindowsAuthentication.
* *TrustServerCertificate*. Tento parametr nastaví hodnotu, která indikuje, jestli je kanál zašifrovaný při obnechání procházení řetězu certifikátů k ověření vztahu důvěryhodnosti. Hodnota může být `$true` nebo `$false` .

Následující příklad vytvoří objekt s informacemi o připojení pro zdrojovou SQL Server s názvem *MySourceSQLServer* s použitím ověřování SQL:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Další příklad ukazuje vytvoření informací o připojení pro spravovanou instanci Azure SQL s názvem ' targetmanagedinstance ':

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>Poskytnout databáze pro projekt migrace

Vytvořte seznam `AzDataMigrationDatabaseInfo` objektů, které určují databáze jako součást projektu Azure Database Migration Service, který lze poskytnout jako parametr pro vytvoření projektu. Pomocí rutiny můžete `New-AzDataMigrationDatabaseInfo` vytvořit `AzDataMigrationDatabaseInfo` .

Následující příklad vytvoří `AzDataMigrationDatabaseInfo` projekt pro databázi **AdventureWorks2016** a přidá ji do seznamu, který má být poskytnut jako parametr pro vytvoření projektu.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Vytvoření objektu projektu

Nakonec můžete vytvořit Azure Database Migration Service projekt s názvem *MyDMSProject* , který se nachází v *východní USA* pomocí `New-AzDataMigrationProject` a přidat dříve vytvořená zdrojová a cílová připojení a seznam databází, které chcete migrovat.

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

Potom vytvořte a spusťte úlohu Azure Database Migration Service. Tato úloha vyžaduje informace o přihlašovacích údajích pro zdroj i cíl i seznam databázových tabulek, které mají být migrovány, a informace, které jsou již součástí projektu vytvořeného jako předpoklad.

### <a name="create-credential-parameters-for-source-and-target"></a>Vytvoření parametrů přihlašovacích údajů pro zdroj a cíl

Vytvořte přihlašovací údaje zabezpečení připojení jako objekt [PSCredential](/dotnet/api/system.management.automation.pscredential) .

Následující příklad ukazuje vytváření *PSCredential* objektů pro zdrojové i cílové připojení a poskytuje hesla jako řetězcové proměnné *$sourcePassword* a *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Vytvoření objektu záložní sdílené složky

Nyní vytvořte objekt sdílení souborů reprezentující místní sdílenou síťovou složku SMB, do které Azure Database Migration Service může převzít zálohy zdrojové databáze pomocí `New-AzDmsFileShare` rutiny.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Vytvořit vybraný databázový objekt

Dalším krokem je výběr zdrojové a cílové databáze pomocí `New-AzDmsSelectedDB` rutiny.

Následující příklad slouží k migraci izolované databáze z SQL Server do spravované instance Azure SQL:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Pokud celá SQL Server instance potřebuje přebírat a přesunout se do spravované instance Azure SQL, zobrazí se následující smyčka pro přijetí všech databází ze zdroje. V následujícím příkladu pro $Server, $SourceUserName a $SourcePassword zadejte zdrojové SQL Server podrobnosti.

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

### <a name="sas-uri-for-azure-storage-container"></a>Identifikátor URI SAS pro kontejner Azure Storage

Vytvořte proměnnou obsahující identifikátor URI SAS, který poskytuje Azure Database Migration Service s přístupem k kontejneru účtu úložiště, do kterého služba odesílá záložní soubory.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

> [!NOTE]
> Azure Database Migration Service nepodporuje použití tokenu SAS na úrovni účtu. Pro kontejner účtu úložiště je nutné použít identifikátor URI SAS. [Zjistěte, jak získat identifikátor URI SAS pro kontejner objektů blob](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

### <a name="additional-configuration-requirements"></a>Další požadavky na konfiguraci

K dispozici je několik dalších požadavků, které je potřeba řešit:


* **Vyberte přihlašovací jména**. Vytvořte seznam přihlašovacích údajů, které se mají migrovat, jak je znázorněno v následujícím příkladu:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > V současné době Azure Database Migration Service podporuje pouze migraci přihlašovacích údajů SQL.

* **Vyberte úlohy agenta**. Vytvořte seznam úloh agenta, které se mají migrovat, jak je znázorněno v následujícím příkladu:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > V současné době Azure Database Migration Service podporuje pouze úlohy s podsystémy T-SQL.



### <a name="create-and-start-the-migration-task"></a>Vytvoření a spuštění úlohy migrace

Pomocí `New-AzDataMigrationTask` rutiny vytvořte a spusťte úlohu migrace.

#### <a name="specify-parameters"></a>Zadat parametry

`New-AzDataMigrationTask`Rutina očekává následující parametry:

* *TaskType*. Očekává se typ úlohy migrace, která se vytvoří pro SQL Server typ migrace spravované instance SQL Azure *MigrateSqlServerSqlDbMi* . 
* *Název skupiny prostředků* Název skupiny prostředků Azure, ve které se má úloha vytvořit
* *ServiceName*. Instance Azure Database Migration Service, ve které chcete vytvořit úlohu.
* *ProjectName*. Název Azure Database Migration Service projektu, ve kterém má být úloha vytvořena. 
* *Název_úlohy*. Název úkolu, který se má vytvořit 
* *SourceConnection*. Objekt AzDmsConnInfo představující zdrojové SQL Server připojení.
* *TargetConnection*. Objekt AzDmsConnInfo, který představuje cílové připojení spravované instance Azure SQL
* *SourceCred*. Objekt [PSCredential](/dotnet/api/system.management.automation.pscredential) pro připojení ke zdrojovému serveru.
* *TargetCred*. Objekt [PSCredential](/dotnet/api/system.management.automation.pscredential) pro připojení k cílovému serveru.
* *SelectedDatabase*. Objekt AzDataMigrationSelectedDB představující mapování zdrojového a cílového databáze.
* *BackupFileShare*. Objekt Shared představující sdílenou síťovou složku, do které může Azure Database Migration Service přebírat zdrojové zálohy databáze.
* *BackupBlobSasUri*. Identifikátor URI SAS, který poskytuje Azure Database Migration Service s přístupem k kontejneru účtu úložiště, do kterého služba odesílá záložní soubory. Zjistěte, jak získat identifikátor URI SAS pro kontejner objektů blob.
* *SelectedLogins*. Seznam vybraných přihlašovacích údajů, které se mají migrovat
* *SelectedAgentJobs*. Seznam vybraných úloh agenta k migraci
* *SelectedLogins*. Seznam vybraných přihlašovacích údajů, které se mají migrovat
* *SelectedAgentJobs*. Seznam vybraných úloh agenta k migraci



#### <a name="create-and-start-a-migration-task"></a>Vytvoření a spuštění úlohy migrace

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


## <a name="monitor-the-migration"></a>Monitorování migrace

K monitorování migrace proveďte následující úlohy.

1. Konsolidujte všechny podrobnosti o migraci do proměnné s názvem $CheckTask.

    K kombinování podrobností o migraci, jako jsou vlastnosti, stav a informace o databázi přidružené k migraci, použijte následující fragment kódu:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. `$CheckTask`K získání aktuálního stavu úlohy migrace použijte proměnnou.

    Chcete-li použít `$CheckTask` proměnnou k získání aktuálního stavu úlohy migrace, můžete sledovat úlohu migrace spuštěnou dotazem na vlastnost stavu úlohy, jak je znázorněno v následujícím příkladu:

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


## <a name="delete-the-instance-of-azure-database-migration-service"></a>Odstranění instance Azure Database Migration Service

Po dokončení migrace můžete instanci Azure Database Migration Service odstranit:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Azure Database Migration Service v článku [co je Azure Database Migration Service?](./dms-overview.md).

Informace o dalších scénářích migrace (páry zdroj/cíl) najdete v [Průvodci migrací databáze](https://datamigration.microsoft.com/)společnosti Microsoft.