---
title: Import souboru BACPAC pro vytvoření databáze v Azure SQL Database
description: Vytvoří novou databázi v Azure SQL Database nebo spravované instanci Azure SQL ze souboru BACPAC.
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/29/2020
ms.openlocfilehash: 2412d3d2851d1b4d251b50ff3068b7dcafcccee8
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642037"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Rychlý Start: Import souboru BACPAC do databáze ve službě Azure SQL Database nebo Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

SQL Serverovou databázi můžete importovat do Azure SQL Database nebo spravované instance SQL pomocí souboru [BacPac](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) . Můžete importovat data ze souboru BACPAC uloženého ve službě Azure Blob Storage (pouze v úložišti úrovně Standard) nebo z místního úložiště v místním umístění. Pokud chcete zajistit rychlejší import s využitím většího množství rychlejších prostředků, škálujte databázi během procesu importu na vyšší úroveň služby a velikost výpočetních prostředků. Po úspěšném dokončení importu pak můžete kapacitu vertikálně snížit.

> [!NOTE]
> Úroveň kompatibility importované databáze je založena na úrovni kompatibility zdrojové databáze.

> [!IMPORTANT]
> Po importu databáze se můžete rozhodnout pro provoz databáze na aktuální úrovni kompatibility (úroveň 100 pro databázi AdventureWorks2008R2) nebo na vyšší úrovni. Další informace o důsledcích a možnostech provozu databáze na konkrétní úrovni kompatibility najdete v tématu [Úroveň kompatibility ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). V tématu věnovaném příkazu [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) najdete také informace o dalších nastaveních na úrovni databáze souvisejících s úrovněmi kompatibility.

## <a name="using-azure-portal"></a>Pomocí webu Azure Portal

Podívejte se na toto video, kde se dozvíte, jak naimportovat ze souboru BACPAC do Azure Portal nebo pokračovat v čtení níže:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Azure Portal](https://portal.azure.com) podporuje *pouze* vytváření izolovaných databází v Azure SQL Database a *pouze* ze souboru BacPac uloženého v úložišti objektů BLOB v Azure.

K migraci databáze do [spravované instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) ze souboru BACPAC použijte SQL Server Management Studio nebo SQLPackage, použití Azure Portal nebo Azure PowerShell není momentálně podporované.

> [!NOTE]
> Počítače zpracovávající požadavky na Import a export odeslané prostřednictvím Azure Portal nebo PowerShellu musí ukládat soubor BACPAC a také dočasné soubory generované Data-Tier aplikačním rozhraním (DacFX). Požadované místo na disku se výrazně liší mezi databázemi se stejnou velikostí a může vyžadovat místo na disku až třikrát velikosti databáze. Počítače, na kterých běží požadavek import/export, mají 450GB místo na místním disku. V důsledku toho se může stát, že některé požadavky selžou s chybou `There is not enough space on the disk` . V takovém případě je možné alternativní řešení spustit sqlpackage.exe v počítači s dostatečným místem na disku. K tomu, abyste se vyhnuli tomuto problému, doporučujeme používat SqlPackage k importu a exportu databází větších než 150 GB.

1. Pro import ze souboru BACPAC do nové izolované databáze pomocí Azure Portal otevřete příslušnou stránku serveru a pak na panelu nástrojů vyberte **importovat databázi**.  

   ![Import1 databáze](./media/database-import/sql-server-import-database.png)

1. Vyberte účet úložiště a kontejner pro soubor BACPAC a pak vyberte soubor BACPAC, ze kterého se má importovat.

1. Zadejte novou velikost databáze (obvykle stejný jako zdroj) a zadejte přihlašovací údaje pro cíl SQL Server. Seznam možných hodnot pro novou databázi v Azure SQL Database najdete v tématu [Create Database](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true).

   ![Import2 databáze](./media/database-import/sql-server-import-database-settings.png)

1. Klikněte na **OK**.

1. Chcete-li monitorovat průběh importu, otevřete stránku serveru databáze a v části **Nastavení** vyberte **Historie importu a exportu**. Po úspěšném dokončení importu bude **dokončený** stav.

   ![Stav importu databáze](./media/database-import/sql-server-import-database-history.png)

1. Pokud chcete ověřit, jestli je databáze na serveru živá, vyberte **databáze SQL** a ověřte, jestli je nová databáze **online**.

## <a name="using-sqlpackage"></a>Použití SqlPackage

Chcete-li importovat databázi SQL Server pomocí nástroje příkazového řádku [SqlPackage](/sql/tools/sqlpackage) , přečtěte si téma [Import parametrů a vlastností](/sql/tools/sqlpackage#import-parameters-and-properties). K [disSQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Data Tools for Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt) patří SqlPackage. Nejnovější [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) můžete také stáhnout z webu služby Stažení softwaru. 

Pro škálování a výkon doporučujeme místo použití Azure Portal používat SqlPackage ve většině produkčních prostředí. Blog týmu pro poradenské zákazníky SQL Server o migraci pomocí `BACPAC` souborů najdete v tématu [migrace z SQL Server na Azure SQL Database pomocí souborů BacPac](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Model zřizování založený na DTU podporuje pro každou vrstvu výběr hodnot maximální velikosti databáze. Při importu databáze [použijte jednu z těchto podporovaných hodnot](/sql/t-sql/statements/create-database-transact-sql). 

Následující příkaz SqlPackage importuje databázi **AdventureWorks2008R2** z místního úložiště na logický SQL Server s názvem **mynewserver20170403**. Vytvoří novou databázi s názvem **myMigratedDatabase** s úrovní služeb **Premium** a cílem služby **P6** . Změňte tyto hodnoty tak, jak jsou vhodné pro vaše prostředí.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Pokud se chcete připojit k Azure SQL Database z za podnikovým firewallem, musí mít brána firewall otevřený port 1433. Chcete-li se připojit k spravované instanci SQL, je nutné mít [připojení typu Point-to-site](../managed-instance/point-to-site-p2s-configure.md) nebo připojení Express Route.

Tento příklad ukazuje, jak importovat databázi pomocí SqlPackage s univerzálním ověřováním služby Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Pomocí prostředí PowerShell

> [!NOTE]
> [Spravovaná instance SQL](../managed-instance/sql-managed-instance-paas-overview.md) v současné době nepodporuje migraci databáze do databáze instancí ze souboru BACPAC pomocí Azure PowerShell. Pro import do spravované instance SQL použijte SQL Server Management Studio nebo SQLPackage.

> [!NOTE]
> Počítače zpracovávající požadavky na Import a export odeslané prostřednictvím portálu nebo PowerShellu musí ukládat soubor BacPac a také dočasné soubory vygenerované Data-Tier aplikační Framework (DacFX). Požadované místo na disku se výrazně liší mezi databáze se stejnou velikostí a může trvat až 3 časy velikosti databáze. Počítače, na kterých běží požadavek import/export, mají 450GB místo na místním disku. V důsledku toho se může stát, že některé žádosti selžou a na disku není dost místa. V takovém případě je možné alternativní řešení spustit sqlpackage.exe v počítači s dostatečným místem na disku. Při importu a exportu databází větších než 150 GB použijte SqlPackage, abyste se tomuto problému vyhnuli.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Modul Azure Resource Manager PowerShellu (RM) je stále podporován, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. V modulu AzureRM bude i nadále docházet k opravám chyb až do prosince 2020.  Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Další informace o kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

K odeslání žádosti o import databáze do Azure použijte rutinu [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) . V závislosti na velikosti databáze může dokončení importu nějakou dobu trvat. Model zřizování založený na DTU podporuje pro každou vrstvu výběr hodnot maximální velikosti databáze. Při importu databáze [použijte jednu z těchto podporovaných hodnot](/sql/t-sql/statements/create-database-transact-sql). 

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

Pomocí rutiny [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) můžete kontrolovat průběh importu. Spuštění rutiny hned poté, co požadavek obvykle vrátí `Status: InProgress` . Import je dokončen po zobrazení `Status: Succeeded` .

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazu [AZ-SQL-DB-import](/cli/azure/sql/db#az-sql-db-import) odešlete žádost o import databáze do Azure. V závislosti na velikosti databáze může dokončení importu nějakou dobu trvat. Model zřizování založený na DTU podporuje pro každou vrstvu výběr hodnot maximální velikosti databáze. Při importu databáze [použijte jednu z těchto podporovaných hodnot](/sql/t-sql/statements/create-database-transact-sql). 

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Další příklad skriptu najdete v tématu [Import databáze ze souboru BacPac](scripts/import-from-bacpac-powershell.md).

## <a name="cancel-the-import-request"></a>Zrušit žádost o import

V následujícím příkladu příkazu PowerShellu použijte [operace Database Operations – Cancel API](/rest/api/sql/databaseoperations/cancel) nebo PowerShell [stop-AzSqlDatabaseActivity](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity).

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```


## <a name="limitations"></a>Omezení

- Import do databáze v elastickém fondu se nepodporuje. Můžete importovat data do jednoúčelové databáze, kterou pak můžete přesunout do elastického fondu.
- Služba import exportu nefunguje, když je povolený přístup ke službám Azure. Problém ale můžete obejít tak, že ručně spustíte sqlpackage.exe z virtuálního počítače Azure nebo exportujete přímo do kódu pomocí rozhraní API DACFx.
- Import nepodporuje zadání redundance záložního úložiště při vytváření nové databáze a vytváří se s výchozí geograficky redundantní redundancí úložiště záloh. Pro alternativní řešení nejprve vytvořte prázdnou databázi s požadovanou redundancí záložního úložiště pomocí Azure Portal nebo PowerShellu a pak importujte BACPAC do této prázdné databáze. 

> [!NOTE]
> Služba Azure SQL Database konfigurovatelný záložní úložiště je v současnosti dostupná jenom ve verzi Public Preview v oblasti Azure jihovýchodní Asie.

## <a name="import-using-wizards"></a>Import pomocí průvodců

Můžete také použít tyto průvodce.

- [Průvodce importem aplikace na datové vrstvě v SQL Server Management Studio](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Průvodce importem a exportem SQL Server](/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak se připojit k databázi a dotazovat se na ni v Azure SQL Database, najdete v tématu [rychlý Start: Azure SQL Database: použití SQL Server Management Studio k připojení a dotazování dat](connect-query-ssms.md).
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Diskuzi o celém procesu migrace databáze SQL Server, včetně doporučení týkajících se výkonu, najdete v tématu [SQL Server migrace databáze na Azure SQL Database](migrate-to-database-from-sql-server.md).
- Informace o tom, jak bezpečně spravovat a sdílet klíče úložiště a signatury sdíleného přístupu, najdete v tématu [Azure Storage Průvodce zabezpečením](../../storage/blobs/security-recommendations.md).