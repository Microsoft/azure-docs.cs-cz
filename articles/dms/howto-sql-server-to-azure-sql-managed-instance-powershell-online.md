---
title: 'PowerShell: migrace SQL Server do spravované instance SQL online'
titleSuffix: Azure Database Migration Service
description: Přečtěte si, jak online migrovat z SQL Server do spravované instance Azure SQL pomocí Azure PowerShell a Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98697780"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>Migrace SQL Server na spravovanou instanci SQL online pomocí prostředí PowerShell & Azure Database Migration Service

V tomto článku jste online migraci databáze **Adventureworks2016** obnovili do místní instance SQL Server 2005 nebo vyšší do spravované instance Azure SQL sql pomocí Microsoft Azure PowerShell. Databáze můžete migrovat z SQL Server instance do spravované instance SQL pomocí `Az.DataMigration` modulu v Microsoft Azure PowerShell.

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
>
> * Vytvořte skupinu prostředků.
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace v instanci Azure Database Migration Service.
> * Spusťte migraci online.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje kroky pro online migraci, ale je také možné migrovat [offline](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md).


## <a name="prerequisites"></a>Předpoklady

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

    > [!IMPORTANT]
    > Pro online migrace musíte mít již nastavené přihlašovací údaje Azure Active Directory. Další informace najdete v článku [použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) příkazu.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *východní USA* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>Vytvoření instance DMS

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
Definujte zdrojové a cílové připojovací řetězce připojení.

Následující skript definuje podrobnosti o zdrojovém SQL Server připojení: 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

Následující skript definuje podrobnosti připojení cílové instance SQL: 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>Definovat zdrojové a cílové mapování databáze

Poskytnout databáze, které se mají migrovat v tomto projektu migrace

Následující skript mapuje zdrojovou databázi do příslušné nové databáze v cílové spravované instanci SQL se zadaným názvem.

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

V případě více databází přidejte seznam databází do výše uvedeného skriptu pomocí následujícího formátu:

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>Vytvoření projektu DMS

V rámci instance DMS můžete vytvořit Azure Database Migration Service projekt.

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>Vytvoření objektu záložní sdílené složky

Nyní vytvořte objekt sdílení souborů reprezentující místní sdílenou síťovou složku SMB, ke které Azure Database Migration Service může převzít zálohy zdrojové databáze pomocí rutiny New-AzDmsFileShare.

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>Definovat Azure Storage 

Vyberte kontejner Azure Storage, který se má použít pro migraci: 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Konfigurace aplikace Azure Active Directory

Zadejte požadované podrobnosti Azure Active Directory pro online migraci spravované instance SQL: 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>Vytvoření a spuštění úlohy migrace

Potom vytvořte a spusťte úlohu Azure Database Migration Service. Zavolejte zdroj a cíl pomocí proměnných a seznam tabulek databáze, které chcete migrovat: 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

Následující příklad vytvoří a spustí úlohu migrace online: 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

Další informace najdete v tématu [New-AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask).

## <a name="monitor-the-migration"></a>Monitorování migrace

K monitorování migrace proveďte následující úlohy.

### <a name="check-the-status-of-task"></a>Zkontroluje stav úlohy.

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

Seznam chyb získáte pomocí následujících kroků:-

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>Provádění přímou migraci 

V případě online migrace se provádí úplné zálohování a obnovení databází a pak pokračuje v obnovování protokolů transakcí uložených v BackupFileShare.

Když se databáze ve spravované instanci SQL Azure aktualizuje s nejnovějšími daty a je synchronizovaná se zdrojovou databází, můžete provést přímou migraci.

V následujícím příkladu se dokončí cutover\migration. Uživatelé vyvolají tento příkaz podle svého uvážení.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Odstraňování instance Azure Database Migration Service

Po dokončení migrace můžete instanci Azure Database Migration Service odstranit:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Další zdroje informací

Informace o dalších scénářích migrace (páry zdroj/cíl) najdete v [Průvodci migrací databáze](https://datamigration.microsoft.com/)společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Azure Database Migration Service v článku [co je Azure Database Migration Service?](./dms-overview.md).