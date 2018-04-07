---
title: Použití modulu Služba migrace databáze Azure v Microsoft Azure PowerShell k migraci serveru SQL Server místním nasazením a Azure SQL DB | Microsoft Docs
description: Další informace pro migraci z místního SQL serveru do Azure SQL pomocí prostředí Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/05/2018
ms.openlocfilehash: d70d39f027df1fba7934c4b752b1dd2100f2e8b9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>Migrovat místní SQL Server k databázi SQL Azure pomocí Azure PowerShell
V tomto článku, migrovat **Adventureworks2012** databáze obnovena do místní instance systému SQL Server 2016 nebo novější verze do Azure SQL Database pomocí Microsoft Azure PowerShell. Databáze můžete migrovat z místní instance systému SQL Server do Azure SQL Database pomocí `AzureRM.DataMigration` modulu v Microsoft Azure PowerShell.

V tomto článku se dozvíte, jak:
> [!div class="checklist"]
> * Vytvořte skupinu prostředků.
> * Vytvoření instance služby migrace databáze Azure.
> * Vytvoření projektu migrace v instanci služby migrace databáze Azure.
> * Spusťte migrace.

## <a name="prerequisites"></a>Požadavky
K dokončení těchto kroků, potřebujete:

- [SQL Server 2016 nebo novější](https://www.microsoft.com/sql-server/sql-server-downloads) (všechny edice)
- Ve výchozím nastavení instalace systému SQL Server Express je zakázán protokol TCP/IP. Povolte podle následujících [pokyny v tomto článku](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Ke konfiguraci vaší [brány Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Instance databáze SQL Azure. Instance databáze SQL Azure můžete vytvořit pomocí následujících podrobností v článku [vytvoření Azure SQL database na portálu Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Pomocník pro migraci dat](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 nebo novější.
- Služba Azure databáze migrace vyžaduje, aby virtuální sítě vytvořené pomocí modelu nasazení Azure Resource Manager, které poskytuje připojení site-to-site k vaší místní zdrojové servery pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Dokončit hodnocení místní databázi a schéma migrace pomocí Pomocníka pro migraci dat, jak je popsáno v článku [ provádění na posouzení migrace systému SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Stáhněte a nainstalujte modul AzureRM.DataMigration z Galerie prostředí PowerShell pomocí bu [rutinu instalace modulu prostředí PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- Pověření použitá pro připojení k instanci systému SQL Server zdrojové musí mít [ovládacího PRVKU serveru](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) oprávnění.
- Pověření použitá pro připojení k cílové databázi SQL Azure instance musí mít oprávnění CONTROL DATABASE na Azure SQL Database cílovým databázím.
- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Přihlaste se k předplatnému Microsoft Azure
Postupujte podle pokynů v článku [Přihlaste se pomocí Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) k přihlášení k předplatnému Azure pomocí prostředí PowerShell.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků, než bude možné vytvořit virtuální počítač.

Vytvořte skupinu prostředků s použitím [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) příkaz. 

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *EastUS* oblast.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby migrace databáze Azure 
Můžete vytvořit novou instanci služby Azure databáze migrace pomocí `New-AzureRmDataMigrationService` rutiny. Tato rutina očekává následující požadované parametry:
- *Název skupiny prostředků Azure*. Můžete použít [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) příkaz pro vytvoření skupiny prostředků Azure jako dříve vidět a zadejte jeho název jako parametr.
- *Název služby*. Řetězec, který odpovídá názvu požadované jedinečné služby pro službu migrace databáze Azure 
- *Umístění*. Určuje umístění služby. Zadat umístění center dat Azure, jako je například západní USA nebo jihovýchodní Asie
- *Sku*. Tento parametr odpovídá název SKJ DMS. Aktuálně podporované Sku názvů *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Identifikátor virtuální podsítě*. Můžete použít rutinu [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) vytvořit podsíť. 

Následující příklad vytvoří služba s názvem *MyDMS* ve skupině prostředků *MyDMSResourceGroup*, který se nachází v *východní USA* oblast pomocí virtuální sítě s názvem *MyVNET* a podsíť s názvem *MySubnet*.

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
Po vytvoření instance služby migrace databáze Azure, vytvořte projekt migrace. Projekt služba migrace databáze Azure vyžaduje informace o připojení pro zdrojové i cílové instance, jak seznam databází, které chcete migrovat jako součást projektu.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Vytvoření objektu informace o připojení databáze pro připojení zdroje a cíle
Objekt informace o připojení databáze můžete vytvořit pomocí `New-AzureRmDmsConnInfo` rutiny. Tato rutina očekává následující parametry:
- *Typ*. Typ připojení k databázi požadovaný, například SQL, Oracle nebo MySQL. Pomocí jazyka SQL pro SQL Server a SQL Azure.
- *Zdroj dat*. Název nebo IP instanci SQL nebo server SQL Azure.
- *AuthType*. Typ ověřování pro připojení, které může být buď SqlAuthentication nebo WindowsAuthentication.
- *TrustServerCertificate* parametr nastaví hodnotu, která určuje, jestli se kanál šifruje při obcházení proti řetěz certifikátů pro ověření vztahu důvěryhodnosti. Hodnota může být true nebo false.

Následující příklad vytvoří objekt informace o připojení pro zdroj SQL serveru s názvem MySQLSourceServer pomocí ověřování sql. 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySQLSourceServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Další příklad ukazuje vytvoření informace o připojení serveru databáze SQL Azure s názvem MySQLAzureTarget pomocí ověřování sql.

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "mysqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Zadejte databáze pro projekt migrace
Vytvoří seznam `AzureRmDataMigrationDatabaseInfo` objekty, které určuje databáze jako součást migrace databáze Azure projektu, který lze zadat jako parametr pro vytvoření projektu. Rutiny `New-AzureRmDataMigrationDatabaseInfo` slouží k vytvoření AzureRmDataMigrationDatabaseInfo. 

Následující příklad vytvoří `AzureRmDataMigrationDatabaseInfo` projektu pro databázi AdventureWorks2016 a přidá ji do seznamu a je třeba zadat jako parametr pro vytvoření projektu.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Vytvoření objektu projektu
Nakonec můžete vytvořit projekt migrace databáze Azure s názvem *MyDMSProject* umístěný v *východní USA* pomocí `New-AzureRmDataMigrationProject` a přidání dříve vytvořenou zdrojové a cílové připojení a seznam databáze pro migraci.

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
Nakonec vytvořte a spusťte úlohy migrace databáze Azure. Azure úloha migrace databáze vyžaduje připojení přihlašovací údaje pro zdroj i cíl a seznam tabulek databáze se tedy migrovat kromě informací o již součástí projektu vytvořili v rámci předpokladů. 

### <a name="create-credential-parameters-for-source-and-target"></a>Vytvoření parametry přihlašovací údaje pro zdroje a cíle
Přihlašovací údaje pro připojení zabezpečení se dá vytvořit jako [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objektu. 

Následující příklad ukazuje vytvoření *PSCredential* objekty pro zdroj i cíl připojení poskytuje hesla jako řetězec proměnné *$sourcePassword* a *$ Cílové_heslo*. 

```powershell
secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Vytvoření tabulky mapu a vyberte zdrojové a cílové parametry pro migraci
Jiný parametr potřebné k migraci je mapování tabulek ze zdroje cíl, který se má migrovat. Vytvořte adresář tabulek, který poskytuje mapování mezi zdrojové a cílové tabulky pro migraci. Následující příklad ilustruje mapování mezi tabulkami zdrojové a cílové schéma lidských zdrojů pro databázi AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Dalším krokem je vybrat zdrojové a cílové databáze a poskytují mapování tabulky, které chcete migrovat jako parametr pomocí `New-AzureRmDmsSqlServerSqlDbSelectedDB` rutiny, jak je znázorněno v následujícím příkladu:

```powershell
$selectedDbs = New-AzureRmDmsSqlServerSqlDbSelectedDB -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Vytvoření a spuštění úlohy migrace

Použití `New-AzureRmDataMigrationTask` rutiny pro vytvoření a spuštění úlohy migrace. Tato rutina očekává následující parametry:
- *TaskType*. Typ úlohy migrace vytvořit pro SQL Server SQL Azure typu migrace *MigrateSqlServerSqlDb* se očekává. 
- *Název skupiny prostředků*. Název skupiny prostředků Azure, ve kterém se má vytvořit úlohu.
- *ServiceName*. Azure instance služby migrace databáze, ve kterém se má vytvořit úlohu.
- *Název projektu*. Název projektu migrace databáze Azure, ve kterém se má vytvořit úlohu. 
- *TaskName*. Název úlohy, který se má vytvořit. 
- *Připojení ke zdroji*. AzureRmDmsConnInfo objekt reprezentující připojení ke zdroji.
- *Cíl připojení*. AzureRmDmsConnInfo objekt reprezentující cíl připojení.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt pro připojení k zdrojového serveru.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt pro připojení k cílovému serveru.
- *SelectedDatabase*. AzureRmDmsSqlServerSqlDbSelectedDB objekt reprezentující mapování zdrojové a cílové databáze.

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
Můžete monitorovat úloha migrace spuštěna vlastnost Stav úlohy, jak je znázorněno v následujícím příkladu:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>Další postup
- Přečtěte si pokyny migrace v Microsoft [příručka k migraci databáze](https://datamigration.microsoft.com/).