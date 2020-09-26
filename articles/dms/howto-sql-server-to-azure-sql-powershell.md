---
title: 'PowerShell: migrace SQL Server do SQL Database'
titleSuffix: Azure Database Migration Service
description: Naučte se migrovat databázi z SQL Server na Azure SQL Database pomocí Azure PowerShell s Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: a2d0ff6810326f7f375595e8dcebbe81b55055ca
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330345"
---
# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-azure-powershell"></a>Migrace databáze SQL Server pro Azure SQL Database pomocí Azure PowerShell

V tomto článku migrujete databázi **Adventureworks2012** obnovenou do místní instance SQL Server 2016 nebo vyšší a Azure SQL Database pomocí Microsoft Azure PowerShell. Databáze můžete migrovat z instance SQL Server do Azure SQL Database pomocí `Az.DataMigration` modulu v Microsoft Azure PowerShell.

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
>
> * Vytvořte skupinu prostředků.
> * Vytvoření instance služby Azure Database Migration Service
> * Vytvořte projekt migrace v instanci Azure Database Migration Service.
> * Spuštění migrace

## <a name="prerequisites"></a>Požadavky

K provedení těchto kroků potřebujete:

* [SQL Server 2016 nebo vyšší](https://www.microsoft.com/sql-server/sql-server-downloads) (jakákoli edice)
* Povolení protokolu TCP/IP, který je ve výchozím nastavení zakázán při instalaci SQL Server Express. Povolte protokol TCP/IP podle článku [Povolení nebo zakázání síťového protokolu serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Ke konfiguraci [brány Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Instance služby Azure SQL Database. Instanci Azure SQL Database můžete vytvořit podle podrobných informací uvedených v článku [Vytvoření databáze v Azure SQL Database v Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 nebo novějším.
* Pokud chcete vytvořit Microsoft Azure Virtual Network pomocí modelu nasazení Azure Resource Manager, který poskytuje Azure Database Migration Service připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Aby bylo dokončeno posouzení místní databáze a migrace schématu pomocí Data Migration Assistant, jak je popsáno v článku, který [provádí hodnocení migrace SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
* Pokud chcete stáhnout a nainstalovat modul AZ. datamigration z Galerie prostředí PowerShell pomocí [rutiny prostředí PowerShell Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1); Nezapomeňte otevřít okno příkazového řádku PowerShellu pomocí příkazu Spustit jako správce.
* Aby bylo zajištěno, že přihlašovací údaje použité pro připojení ke zdrojové SQL Server instance mají oprávnění [Control Server](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* Aby se zajistilo, že přihlašovací údaje použité pro připojení k cílové instanci Azure SQL DB mají oprávnění řídicí databáze pro cílové Azure SQL Database databáze.
* Předplatné Azure. Pokud ho ještě nemáte, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Přihlášení k předplatnému Microsoft Azure

Pomocí pokynů v článku [přihlášení pomocí Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) se přihlaste ke svému předplatnému Azure pomocí PowerShellu.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Než budete moct vytvořit virtuální počítač, vytvořte skupinu prostředků.

Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) .

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *EastUS* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Vytvoření instance služby Azure Database Migration Service

Novou instanci Azure Database Migration Service můžete vytvořit pomocí `New-AzDataMigrationService` rutiny. Tato rutina očekává následující požadované parametry:

* *Název skupiny prostředků Azure*. Pomocí příkazu [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) můžete vytvořit skupinu prostředků Azure, jak je uvedeno výše, a zadat její název jako parametr.
* *Název služby* Řetězec, který odpovídá požadovanému jedinečnému názvu služby pro Azure Database Migration Service 
* *Umístění:* Určuje umístění služby. Zadejte umístění datového centra Azure, například Západní USA nebo jihovýchodní Asie.
* *SKU*. Tento parametr odpovídá názvu SKU DMS. Aktuálně podporovaný název SKU je *GeneralPurpose_4vCores*.
* *Identifikátor virtuální podsítě*. K vytvoření podsítě můžete použít rutinu [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) . 

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

Objekt s informacemi o připojení databáze můžete vytvořit pomocí `New-AzDmsConnInfo` rutiny. Tato rutina očekává následující parametry:

* *ServerType*. Typ požadovaného databázového připojení, například SQL, Oracle nebo MySQL. Použijte SQL pro SQL Server a Azure SQL.
* *Zdroj dat* Název nebo IP instance SQL Server nebo Azure SQL Database.
* *Typ authType*. Typ ověřování pro připojení, který může být buď SqlAuthentication, nebo WindowsAuthentication.
* Parametr *TrustServerCertificate* nastaví hodnotu, která indikuje, jestli je kanál zašifrovaný při obnechání procházení řetězu certifikátů k ověření vztahu důvěryhodnosti. Hodnota může být true nebo false.

Následující příklad vytvoří objekt info o připojení pro zdroj SQL Server s názvem MySourceSQLServer s použitím ověřování SQL:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Následující příklad ukazuje vytvoření informací o připojení pro server s názvem SQLAzureTarget pomocí ověřování SQL:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Poskytnout databáze pro projekt migrace

Vytvořte seznam `AzDataMigrationDatabaseInfo` objektů, které určují databáze jako součást projektu Azure Database Migration, který lze poskytnout jako parametr pro vytvoření projektu. Rutinu `New-AzDataMigrationDatabaseInfo` lze použít k vytvoření AzDataMigrationDatabaseInfo. 

Následující příklad vytvoří `AzDataMigrationDatabaseInfo` projekt pro databázi **AdventureWorks2016** a přidá ji do seznamu, který je k dispozici jako parametr pro vytvoření projektu.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Vytvoření objektu projektu

Nakonec můžete vytvořit projekt Azure Database Migration s názvem *MyDMSProject* , který se nachází v *východní USA* pomocí `New-AzDataMigrationProject` a přidat dříve vytvořená zdrojová a cílová připojení a seznam databází, které chcete migrovat.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
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

Nakonec vytvořte a spusťte úlohu Azure Database Migration. Úloha migrace databáze Azure vyžaduje, aby byly informace o přihlašovacích údajích pro zdrojový i cílový a seznam tabulek databáze migrovány spolu s informacemi, které jsou již k dispozici v projektu vytvořeném jako předpoklad.

### <a name="create-credential-parameters-for-source-and-target"></a>Vytvoření parametrů přihlašovacích údajů pro zdroj a cíl

Přihlašovací údaje zabezpečení připojení se dají vytvořit jako objekt [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) .

Následující příklad ukazuje vytvoření objektů *PSCredential* pro zdrojové i cílové připojení, které poskytuje hesla jako řetězcové proměnné *$sourcePassword* a *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Vytvoření mapy tabulky a výběr zdrojového a cílového parametru pro migraci

Dalším parametrem, který je potřeba pro migraci, je mapování tabulek ze zdroje na cíl migrace. Vytvořte slovník tabulek, který poskytuje mapování mezi zdrojovými a cílovými tabulkami pro migraci. Následující příklad znázorňuje mapování mezi zdrojovými a cílovými tabulkami schéma lidských zdrojů pro databázi AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Dalším krokem je výběr zdrojové a cílové databáze a zadání mapování tabulky pro migraci jako parametru pomocí `New-AzDmsSelectedDB` rutiny, jak je znázorněno v následujícím příkladu:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>Vytvořit úlohu migrace a spustit ji

Pomocí `New-AzDataMigrationTask` rutiny vytvořte a spusťte úlohu migrace. Tato rutina očekává následující parametry:

* *TaskType*. Očekává se typ úlohy migrace, která se má vytvořit pro SQL Server Azure SQL Database typ migrace *MigrateSqlServerSqlDb* . 
* *Název skupiny prostředků* Název skupiny prostředků Azure, ve které se má úloha vytvořit
* *ServiceName*. Instance Azure Database Migration Service, ve které chcete vytvořit úlohu.
* *ProjectName*. Název Azure Database Migration Service projektu, ve kterém má být úloha vytvořena. 
* *Název_úlohy*. Název úkolu, který se má vytvořit 
* *SourceConnection*. Objekt AzDmsConnInfo představující zdrojové SQL Server připojení.
* *TargetConnection*. AzDmsConnInfo objekt představující cílové připojení Azure SQL Database.
* *SourceCred*. Objekt [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) pro připojení ke zdrojovému serveru.
* *TargetCred*. Objekt [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) pro připojení k cílovému serveru.
* *SelectedDatabase*. Objekt AzDataMigrationSelectedDB představující mapování zdrojového a cílového databáze.
* *SchemaValidation*. (volitelný přepínač, parametr Switch) Po migraci provede porovnání informací o schématu mezi zdrojem a cílem.
* *DataIntegrityValidation*. (volitelný přepínač, parametr Switch) Po migraci provede ověření integrity dat na základě kontrolního součtu mezi zdrojem a cílem.
* *QueryAnalysisValidation*. (volitelný přepínač, parametr Switch) Po migraci provede aplikace rychlou a inteligentní analýzu dotazů načtením dotazů ze zdrojové databáze a jejich spuštěním v cíli.

Následující příklad vytvoří a spustí úlohu migrace s názvem myDMSTask:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

Následující příklad vytvoří a spustí stejnou úlohu migrace, jak je uvedeno výše, ale také provede všechna tři ověření:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>Monitorování migrace

Můžete sledovat úlohu migrace spuštěnou dotazem na vlastnost stavu úlohy, jak je znázorněno v následujícím příkladu:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Odstraňuje se instance DMS.

Po dokončení migrace můžete instanci DMS Azure odstranit:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Další krok

* Přečtěte si pokyny k migraci v [Průvodci migrací databáze](https://datamigration.microsoft.com/)společnosti Microsoft.
