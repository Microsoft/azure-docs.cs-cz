---
title: 'Powershell: Migrace serveru SQL Server do databáze SQL'
titleSuffix: Azure Database Migration Service
description: Naučte se migrovat z místního SQL Serveru do Azure SQL Database pomocí Azure PowerShellu se službou Migrace databáze Azure.
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
ms.openlocfilehash: f63f79402b457017257f1762c6ddc7e04c0ee1af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650686"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-using-azure-powershell"></a>Migrace MÍSTNĚ SQL Serveru do Azure SQL Database pomocí Azure PowerShellu

V tomto článku migrujete databázi **Adventureworks2012** obnovenou do místní instance SQL Serveru 2016 nebo vyšší ho do azure sql databáze pomocí Microsoft Azure PowerShellu. Databáze z místní instance SERVERU SQL Server můžete migrovat `Az.DataMigration` do Azure SQL Database pomocí modulu v Prostředí Microsoft Azure PowerShell.

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
>
> * Vytvořte skupinu prostředků.
> * Vytvoření instance služby Azure Database Migration Service
> * Vytvořte projekt migrace v instanci služby Migrace databáze Azure.
> * Spuštění migrace

## <a name="prerequisites"></a>Požadavky

Chcete-li provést tyto kroky, potřebujete:

* [SQL Server 2016 nebo vyšší](https://www.microsoft.com/sql-server/sql-server-downloads) (libovolné vydání)
* Povolení protokolu TCP/IP, který je ve výchozím nastavení zakázán instalací serveru SQL Server Express. Povolte protokol TCP/IP podle článku [Povolit nebo zakázat síťový protokol serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Konfigurace brány [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Instance služby Azure SQL Database. Instanci Azure SQL Database můžete vytvořit podle podrobností v článku [Vytvoření databáze Azure SQL na webu Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* [Pomocník pro migraci dat](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 nebo novější.
* Vytvoření virtuální sítě Microsoft Azure pomocí modelu nasazení Azure Resource Manageru, který poskytuje službě migrace databáze Azure připojení k místním zdrojovým serverům pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
* Dokončení vyhodnocení místní databáze a migrace schématu pomocí Pomocníka pro migraci dat, jak je popsáno v článku [Provedení vyhodnocení migrace serveru SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
* Stažení a instalace modulu Az.DataMigration z galerie prostředí PowerShell pomocí [rutiny Install-Module PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1); Nezapomeňte otevřít příkazové okno powershellu pomocí příkazu spustit jako správce.
* Chcete-li zajistit, že pověření použitá pro připojení ke zdrojové instanci serveru SQL Server mají oprávnění [ŘÍDICÍHO SERVERU.](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)
* Chcete-li zajistit, že přihlašovací údaje používané pro připojení k cílové instanci Azure SQL DB má oprávnění databáze CONTROL databáze na cílové databáze Azure SQL Database.
* Předplatné Azure. Pokud ho nemáte, vytvořte si [účet zdarma,](https://azure.microsoft.com/free/) než začnete.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Přihlášení k předplatnému Microsoft Azure

Pomocí pokynů v článku [Přihlaste se pomocí Azure PowerShellu](https://docs.microsoft.com/powershell/azure/authenticate-azureps) a přihlaste se k předplatnému Azure pomocí PowerShellu.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Před vytvořením virtuálního počítače vytvořte skupinu prostředků.

Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti *EastUS.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Vytvoření instance služby Migrace databáze Azure

Novou instanci služby Migrace databáze `New-AzDataMigrationService` Azure můžete vytvořit pomocí rutiny. Tato rutina očekává následující požadované parametry:

* *Název skupiny prostředků Azure*. Příkaz [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) můžete použít k vytvoření skupiny prostředků Azure, jak bylo dříve zobrazeno, a zadat její název jako parametr.
* *Název služby*. Řetězec, který odpovídá požadovanému jedinečnému názvu služby pro službu Migrace databáze Azure 
* *Umístění*. Určuje umístění služby. Určení umístění datového centra Azure, například Západní USA nebo Jihovýchodní Asie
* *Sku*. Tento parametr odpovídá názvu DMS Sku. Aktuálně podporovaný název sku je *GeneralPurpose_4vCores*.
* *Identifikátor virtuální podsítě*. K vytvoření podsítě můžete použít rutinu [New-AzVirtualNetworkSubnetConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 

Následující příklad vytvoří službu s názvem *MyDMS* ve skupině prostředků *MyDMSResourceGroup* umístěné v oblasti *USA – východ* pomocí virtuální sítě s názvem *MyVNET* a podsítě *MySubnet*.

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

Objekt Informace o připojení k databázi můžete vytvořit pomocí rutiny. `New-AzDmsConnInfo` Tato rutina očekává následující parametry:

* *ServerType*. Požadovaný typ připojení k databázi, například SQL, Oracle nebo MySQL. Použijte SQL pro SQL Server a Azure SQL.
* *Zdroj dat*. Název nebo IP instance serveru SQL Server nebo databáze Azure SQL.
* *AuthType*. Typ ověřování pro připojení, který může být buď SqlAuthentication nebo WindowsAuthentication.
* *Parametr TrustServerCertificate* nastaví hodnotu, která označuje, zda je kanál šifrován při vynechání chůze řetězu certifikátů za účelem ověření důvěryhodnosti. Hodnota může být true nebo false.

Následující příklad vytvoří objekt Informace o připojení pro zdrojový SQL Server s názvem MySourceSQLServer pomocí ověřování SQL:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Další příklad ukazuje vytvoření informací o připojení pro databázový server Azure SQL s názvem SQLAzureTarget pomocí ověřování SQL:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Poskytnout databáze pro projekt migrace

Vytvořte seznam `AzDataMigrationDatabaseInfo` objektů, který určuje databáze jako součást projektu migrace databáze Azure, který lze poskytnout jako parametr pro vytvoření projektu. Rutina `New-AzDataMigrationDatabaseInfo` lze vytvořit AzDataMigrationDatabaseInfo. 

Následující příklad `AzDataMigrationDatabaseInfo` vytvoří projekt pro databázi **AdventureWorks2016** a přidá jej do seznamu, který má být poskytnut jako parametr pro vytvoření projektu.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Vytvoření objektu projektu

Nakonec můžete vytvořit projekt migrace databáze Azure s názvem *MyDMSProject* umístěný v *USA – východní USA* pomocí `New-AzDataMigrationProject` a přidáním dříve vytvořených zdrojových a cílových připojení a seznamu databází, které chcete migrovat.

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

Nakonec vytvořte a spusťte úlohu migrace databáze Azure. Úloha migrace databáze Azure vyžaduje informace o pověření připojení pro zdroj ové i cílové a seznam databázových tabulek, které mají být migrovány kromě informací, které již byly poskytnuty s projektem vytvořeným jako předpoklad.

### <a name="create-credential-parameters-for-source-and-target"></a>Vytvoření parametrů pověření pro zdroj a cíl

Pověření zabezpečení připojení lze vytvořit jako objekt [PSCredential.](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)

Následující příklad ukazuje vytvoření objektů *PSCredential* pro zdrojová i cílová připojení poskytující hesla jako proměnné řetězce *$sourcePassword* a *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Vytvoření mapy tabulky a výběr zdrojových a cílových parametrů pro migraci

Dalším parametrem potřebným pro migraci je mapování tabulek ze zdroje na cíl, který má být migrován. Vytvořte slovník tabulek, který poskytuje mapování mezi zdrojovými a cílovými tabulkami pro migraci. Následující příklad ilustruje mapování mezi zdrojové a cílové tabulky schéma lidských zdrojů pro databázi AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Dalším krokem je výběr zdrojové a cílové databáze a poskytnout mapování tabulky `New-AzDmsSelectedDB` pro migraci jako parametr pomocí rutiny, jak je znázorněno v následujícím příkladu:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>Vytvoření úlohy migrace a jeho spuštění

Pomocí `New-AzDataMigrationTask` rutiny vytvořte a spusťte úlohu migrace. Tato rutina očekává následující parametry:

* *Typ úkolu*. Typ úlohy migrace, která má být pro SQL Server do typu migrace databáze Azure SQL *typu MigrateSqlServerSqlDb* očekává. 
* *Název skupiny prostředků*. Název skupiny prostředků Azure, ve kterém chcete vytvořit úkol.
* *ServiceName*. Instance služby Migrace databáze Azure, ve které chcete úlohu vytvořit.
* *Název_projektu*. Název projektu služby Migrace databáze Azure, ve kterém chcete vytvořit úkol. 
* *Název_úkolu*. Název úkolu, který má být vytvořen. 
* *SourceConnection*. Objekt AzDmsConnInfo představující zdrojové připojení k serveru SQL Server.
* *TargetConnection*. Objekt AzDmsConnInfo představující cílové připojení databáze Azure SQL Database.
* *SourceCred*. [Objekt PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) pro připojení ke zdrojovému serveru.
* *Cílcred*. [Objekt PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) pro připojení k cílovému serveru.
* *Vybraná databáze*. Objekt AzDataMigrationSelectedDB představující mapování zdrojové a cílové databáze.
* *Ověření schématu*. (volitelně, parametr přepínače) Po migraci provede porovnání informací o schématu mezi zdrojem a cílem.
* *Ověření identity dataintegrity .* (volitelně, parametr přepínače) Po migraci provede ověření integrity dat na základě kontrolního součtu mezi zdrojem a cílem.
* *QueryAnalysisValidation*. (volitelně, parametr přepínače) Po migraci provede rychlou a inteligentní analýzu dotazů načtením dotazů ze zdrojové databáze a provede je v cíli.

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

Úlohu migrace můžete sledovat tak, že se podíváte na vlastnost stavu úlohy, jak je znázorněno v následujícím příkladu:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Odstranění instance DMS

Po dokončení migrace můžete odstranit instanci Azure DMS:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Další krok

* Přečtěte si pokyny k migraci v [Příručce pro migraci databáze](https://datamigration.microsoft.com/)společnosti Microsoft .
