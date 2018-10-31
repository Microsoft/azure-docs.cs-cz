---
title: Migrace SQL serveru do Azure SQL Database pomocí služby Database Migration Service a Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak migrovat z místního SQL serveru do služby Azure SQL Database pomocí prostředí Azure PowerShell.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 0a4e88ed52e2da814d1b10dbc695c9c61b899ef1
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248192"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-using-azure-powershell"></a>Migrace místního SQL serveru do Azure SQL Database pomocí prostředí Azure PowerShell
V tomto článku, migrujete **Adventureworks2012** databáze obnovit k místní instanci systému SQL Server 2016 nebo novějším ke službě Azure SQL Database pomocí prostředí Azure PowerShell. Můžete migrovat databáze z místní instance systému SQL Server do Azure SQL Database s použitím `AzureRM.DataMigration` modulu v prostředí Azure PowerShell.

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Vytvořte skupinu prostředků.
> * Vytvoření instance služby Azure Database Migration Service
> * Vytvořte projekt migrace v instanci Azure Database Migration Service.
> * Spuštění migrace

## <a name="prerequisites"></a>Požadavky
K dokončení těchto kroků budete potřebovat:

- [SQL Server 2016 nebo novější](https://www.microsoft.com/sql-server/sql-server-downloads) (libovolná edice)
- Chcete-li povolit protokol TCP/IP, který je zakázaný ve výchozím nastavení se instalaci systému SQL Server Express. Povolení protokolu TCP/IP pomocí následujícího článku [povolení nebo zakázání síťového protokolu serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Ke konfiguraci vaší [brány Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Instance Azure SQL Database. Můžete vytvořit instanci Azure SQL Database pomocí následujících podrobností v článku [vytvořit databázi Azure SQL na webu Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 nebo novější.
- K vytvoření virtuální sítě pomocí modelu nasazení Azure Resource Manageru, které poskytuje Azure Database Migration Service pomocí připojení site-to-site k vašim serverům místní zdroj pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- K dokončení posouzení místní databáze a schéma migraci pomocí Pomocníka s migrací dat, jak je popsáno v článku [ provádění posouzení migrace systému SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Stáhnout a nainstalovat modul AzureRM.DataMigration z Galerie prostředí PowerShell pomocí [rutinu Powershellu Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1); Ujistěte se, otevřete příkazové okno prostředí powershell použití příkazu Spustit jako správce.
- Ujistěte se, že pověření používaná k připojení ke zdrojové instanci SQL serveru má [ovládacího PRVKU serveru](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) oprávnění.
- Chcete-li zajistit přihlašovací údaje použité pro připojení k cílové službě Azure SQL DB instance má oprávnění CONTROL DATABASE na cílové databáze Azure SQL Database.
- Předplatné Azure. Pokud ho nemáte, vytvořte [bezplatné](https://azure.microsoft.com/free/) účtu než začnete.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Přihlaste se k předplatnému Microsoft Azure
Postupujte podle pokynů v článku [přihlášení pomocí Azure Powershellu](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) pro přihlášení ke svému předplatnému Azure pomocí prostředí PowerShell.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků, než budete moct vytvořit virtuální počítač.

Vytvořte skupinu prostředků s použitím [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) příkazu. 

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *EastUS* oblasti.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Vytvoření instance služby Azure Database Migration Service 
Můžete vytvořit novou instanci služby Azure Database Migration Service pomocí `New-AzureRmDataMigrationService` rutiny. Tato rutina očekává, že následující povinné parametry:
- *Název skupiny prostředků Azure*. Můžete použít [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) příkazu vytvořte skupinu prostředků Azure, jak bylo dříve uvedeno a zadejte jeho název jako parametr.
- *Název služby*. Řetězec, který odpovídá název požadovaného jedinečná služba Azure Database Migration Service 
- *Umístění*. Určuje umístění služby. Zadejte umístění centra služby Azure data, třeba západní USA nebo jihovýchodní Asie
- *Skladová položka*. Tento parametr odpovídá názvu DMS Sku. Názvy aktuálně podporované skladové položky jsou *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Identifikátor virtuální podsítě*. Můžete použít rutinu [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) vytvořit podsíť. 

Následující příklad vytvoří službu s názvem *MyDMS* ve skupině prostředků *MyDMSResourceGroup* umístěné v *USA – východ* pomocí virtuální sítě s názvem oblasti *MyVNET* a podsítě s názvem *MySubnet*.

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace
Po vytvoření instance služby Azure Database Migration Service, vytvořte projekt migrace. Projekt Azure Database Migration Service vyžaduje informace o připojení pro obě instance zdroje a cíle, jakož i seznam databází, které chcete migrovat v rámci projektu.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Vytvoří objekt informace o připojení databáze pro zdroj a cíl připojení
Informace o připojení databáze objekt můžete vytvořit pomocí `New-AzureRmDmsConnInfo` rutiny. Tato rutina očekává, že následující parametry:
- *ServerType*. Typ připojení k databázi vyžádaný, například SQL, Oracle nebo MySQL. Použití SQL pro SQL Server a Azure SQL.
- *Zdroj dat*. Název nebo IP adresu instance systému SQL Server nebo databázi Azure SQL.
- *Typ AuthType*. Typ ověřování pro připojení, který může být SqlAuthentication nebo WindowsAuthentication.
- *TrustServerCertificate* parametr nastaví hodnotu určující, zda kanál se šifrují při obcházení walking řetěz certifikátů pro ověření vztahu důvěryhodnosti. Hodnota může být true nebo false.

Následující příklad vytvoří objekt informace o připojení pro zdroj SQL serveru s názvem MySourceSQLServer pomocí ověřování sql: 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Následující příklad ukazuje vytvoření informace o připojení serveru databáze Azure SQL s názvem SQLAzureTarget pomocí ověřování sql:

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Zadejte projekt migrace databáze
Vytvoření seznamu `AzureRmDataMigrationDatabaseInfo` objekty, které určuje databáze Azure Database Migration v rámci projektů, které lze zadat jako parametr pro vytvoření projektu. Rutina `New-AzureRmDataMigrationDatabaseInfo` slouží k vytvoření AzureRmDataMigrationDatabaseInfo. 

Následující příklad vytvoří `AzureRmDataMigrationDatabaseInfo` projektu pro **AdventureWorks2016** databáze a přidá ji do seznamu zadat jako parametr pro vytvoření projektu.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Vytvořte objekt projektu
Nakonec můžete vytvořit projekt Azure Database Migration názvem *MyDMSProject* umístěné v *USA – východ* pomocí `New-AzureRmDataMigrationProject` a přidání dříve vytvořený zdroj a cíl připojení a seznam databáze k migraci.

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Vytvoření a spuštění úlohy migrace
Nakonec vytvořte a spusťte úlohy migrace databáze Azure. Azure Database Migration úloha vyžaduje připojení přihlašovací údaje pro zdroj a cíl a seznam tabulek databáze k migraci spolu s informacemi o již součástí projektu vytvořeného jako předpoklad. 

### <a name="create-credential-parameters-for-source-and-target"></a>Vytvoření parametry přihlašovací údaje pro zdroj a cíl
Je možné vytvořit přihlašovací údaje pro připojení zabezpečení jako [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektu. 

Následující příklad ukazuje vytvoření objektu *PSCredential* objekty pro zdroj a cíl připojení poskytuje hesla jako řetězec proměnné *$sourcePassword* a *$ Cílové_heslo*. 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Vytvořte mapu tabulce a vybrat zdrojové a cílové parametry pro migraci
Jiný parametr potřebné k migraci je mapování tabulek ze zdroje do cíle, které chcete migrovat. Vytvořte adresář tabulek, který poskytuje mapování mezi zdrojem a cílem tabulky k migraci. Následující příklad ukazuje, lidské zdroje schéma databáze AdventureWorks 2016 mapování mezi zdrojem a cílem tabulky.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Dalším krokem je vybrat zdrojovou a cílovou databázi a poskytuje mapování tabulky k migraci jako parametr pomocí `New-AzureRmDmsSelectedDB` rutiny, jak je znázorněno v následujícím příkladu:

```powershell
$selectedDbs = New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Vytvoření a spuštění úlohy migrace

Použití `New-AzureRmDataMigrationTask` rutina pro vytvoření a spuštění úlohy migrace. Tato rutina očekává, že následující parametry:
- *TaskType*. Typ úlohy migrace k vytvoření SQL serveru do Azure SQL Database, typ migrace *MigrateSqlServerSqlDb* očekává. 
- *Název skupiny prostředků*. Název skupiny prostředků Azure, ve kterém chcete vytvořit úlohu.
- *ServiceName*. Instance Azure Database Migration Service ve kterém chcete vytvořit úlohu.
- *ProjectName*. Název projektu Azure Database Migration Service, ve kterém chcete vytvořit úlohu. 
- *TaskName*. Název úkolu, který chcete vytvořit. 
- *SourceConnection*. AzureRmDmsConnInfo objekt představující připojení ke zdroji systému SQL Server.
- *TargetConnection*. AzureRmDmsConnInfo objekt představující cíl připojení Azure SQL Database.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektu pro připojení ke zdrojovému serveru.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektu pro připojení k cílovému serveru.
- *SelectedDatabase*. AzureRmDataMigrationSelectedDB objekt reprezentující mapování zdrojové a cílové databázi.

Následující příklad vytvoří a spustí úlohu migrace s názvem myDMSTask:

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
```

## <a name="monitor-the-migration"></a>Monitorování migrace
Můžete monitorovat úlohy migrace s dotazem na vlastnost stavu úkolu, jak je znázorněno v následujícím příkladu:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>Další postup
- Přečtěte si pokyny k migraci v Microsoftu [Průvodce migrací databází](https://datamigration.microsoft.com/).