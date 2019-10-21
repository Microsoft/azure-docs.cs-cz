---
title: Import souboru BACPAC pro vytvoření databáze SQL Azure | Microsoft Docs
description: Vytvořte databázi SQL newAzure importem souboru BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 5e0f3ccafa449cc431166d5b4bd4135a1d1ac97c
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675883"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Rychlý Start: Import souboru BACPAC do databáze v Azure SQL Database

SQL Serverovou databázi můžete importovat do databáze v Azure SQL Database pomocí souboru [BacPac](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) . Data můžete importovat z `BACPAC` souboru uloženého v Azure Blob Storage (jenom úložiště úrovně Standard) nebo z místního úložiště v místním umístění. Aby se maximalizovala rychlost importu tím, že poskytuje více a rychlejší prostředky, během procesu importu Škálujte databázi na vyšší úroveň služby a výpočetní velikost. Po úspěšném dokončení importu pak můžete horizontální navýšení kapacity snížit.

> [!NOTE]
> Úroveň kompatibility importované databáze je založena na úrovni kompatibility zdrojové databáze.
> [!IMPORTANT]
> Po importu databáze se můžete rozhodnout pro provoz databáze na aktuální úrovni kompatibility (úroveň 100 pro databázi AdventureWorks2008R2) nebo na vyšší úrovni. Další informace o důsledcích a možnostech provozu databáze na konkrétní úrovni kompatibility najdete v tématu [Úroveň kompatibility ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). V tématu věnovaném příkazu [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) najdete také informace o dalších nastaveních na úrovni databáze souvisejících s úrovněmi kompatibility.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Import ze souboru BACPAC v Azure Portal

[Azure Portal](https://portal.azure.com) podporuje *pouze* vytváření izolovaných databází v Azure SQL Database a *pouze* ze souboru BacPac uloženého v úložišti objektů BLOB v Azure.

Migrace databáze do [spravované instance](sql-database-managed-instance.md) ze souboru BACPAC pomocí Azure PowerShell není aktuálně podporována. Místo toho použijte SQL Server Management Studio nebo SQLPackage.

> [!NOTE]
> Počítače zpracovávající požadavky na Import a export odeslané prostřednictvím Azure Portal nebo PowerShellu musí ukládat soubor BACPAC a také dočasné soubory generované rozhraním Application Framework (DacFX) na datové vrstvě. Požadované místo na disku se výrazně liší mezi databázemi se stejnou velikostí a může vyžadovat místo na disku až třikrát velikosti databáze. Počítače, na kterých běží požadavek import/export, mají 450GB místo na místním disku. V důsledku toho se může stát, že některé požadavky selžou s chybou `There is not enough space on the disk`. V takovém případě je alternativním řešením spustit SqlPackage. exe na počítači s dostatečným místem na místním disku. K tomu, abyste se vyhnuli tomuto problému, doporučujeme používat [SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) k importu a exportu databází větších než 150 GB.
 
1. Pokud chcete importovat ze souboru BACPAC do nové izolované databáze pomocí Azure Portal, otevřete příslušnou stránku databázového serveru a pak na panelu nástrojů vyberte **importovat databázi**.  

   ![Import1 databáze](./media/sql-database-import/import1.png)

2. Vyberte účet úložiště a kontejner pro soubor BACPAC a pak vyberte soubor BACPAC, ze kterého se má importovat.
3. Zadejte novou velikost databáze (obvykle stejný jako zdroj) a zadejte přihlašovací údaje pro cíl SQL Server. Seznam možných hodnot pro novou databázi SQL Azure najdete v tématu [Vytvoření databáze](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Import2 databáze](./media/sql-database-import/import2.png)

4. Klikněte na **OK**.

5. Chcete-li monitorovat průběh importu, otevřete stránku serveru databáze a v části **Nastavení**vyberte **Historie importu a exportu**. Po úspěšném dokončení importu bude **dokončený** stav.

   ![Stav importu databáze](./media/sql-database-import/import-status.png)

6. Pokud chcete ověřit, jestli je databáze na databázovém serveru živá, vyberte **databáze SQL** a ověřte, jestli je nová databáze **online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Import ze souboru BACPAC pomocí SqlPackage

Chcete-li importovat databázi SQL Server pomocí nástroje příkazového řádku [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) , přečtěte si téma [Import parametrů a vlastností](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage má nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Nejnovější [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) můžete také stáhnout z webu služby Stažení softwaru.

Pro škálování a výkon doporučujeme místo použití Azure Portal používat SqlPackage ve většině produkčních prostředí. Blog týmu pro poradenské zákazníky SQL Server o migraci pomocí souborů `BACPAC` najdete v tématu [migrace z SQL Server na Azure SQL Database pomocí souborů BacPac](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Následující příkaz SqlPackage importuje databázi **AdventureWorks2008R2** z místního úložiště na server Azure SQL Database s názvem **mynewserver20170403**. Vytvoří novou databázi s názvem **myMigratedDatabase** s úrovní služeb **Premium** a cílem služby **P6** . Změňte tyto hodnoty tak, jak jsou vhodné pro vaše prostředí.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Pokud se chcete připojit k SQL Database serveru, který spravuje izolovanou databázi za podnikovou bránou firewall, musí mít brána firewall otevřený port 1433. Chcete-li se připojit ke spravované instanci, je nutné mít [připojení typu Point-to-site](sql-database-managed-instance-configure-p2s.md) nebo připojení Express Route.
>

Tento příklad ukazuje, jak importovat databázi pomocí SqlPackage s univerzálním ověřováním služby Active Directory.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Import do jedné databáze ze souboru BACPAC pomocí prostředí PowerShell

> [!NOTE]
> [Spravovaná instance](sql-database-managed-instance.md) v současné době nepodporuje migraci databáze do databáze instancí ze souboru BACPAC pomocí Azure PowerShell. Chcete-li importovat do spravované instance, použijte SQL Server Management Studio nebo SQLPackage.

> [!NOTE]
> Počítače zpracovávající požadavky import/export odeslané prostřednictvím portálu nebo PowerShellu musí ukládat soubor BacPac a také dočasné soubory vygenerované rozhraním Application Framework (DacFX) na datové vrstvě. Požadované místo na disku se výrazně liší mezi databáze se stejnou velikostí a může trvat až 3 časy velikosti databáze. Počítače, na kterých běží požadavek import/export, mají 450GB místo na místním disku. V důsledku toho se může stát, že některé žádosti selžou a na disku není dost místa. V takovém případě je alternativním řešením spustit SqlPackage. exe na počítači s dostatečným místem na místním disku. Při importu a exportu databází větších než 150 GB použijte [SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) , abyste se tomuto problému vyhnuli.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

K odeslání žádosti o import databáze službě Azure SQL Database použijte rutinu [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) . V závislosti na velikosti databáze může dokončení importu nějakou dobu trvat.

 ```powershell
 $importRequest = New-AzSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Pomocí rutiny [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) můžete kontrolovat průběh importu. Spuštění rutiny hned poté, co požadavek obvykle vrátí **stav: probíhá zpracování**. Import je dokončen po zobrazení **stavu: úspěšné**.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
> Další příklad skriptu najdete v tématu [Import databáze ze souboru BacPac](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Omezení

Import do databáze v elastickém fondu není podporován. Data můžete importovat do izolované databáze a pak databázi přesunout do elastického fondu.

## <a name="import-using-wizards"></a>Import pomocí průvodců

Můžete také použít tyto průvodce.

- [Průvodce importem aplikace na datové vrstvě v SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Průvodce importem a exportem SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak se připojit k importovaným SQL Database a dotazování na ně, najdete v tématu [rychlý Start: Azure SQL Database: použití SQL Server Management Studio k připojení a dotazování dat](sql-database-connect-query-ssms.md).
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Diskuzi o celém procesu migrace databáze SQL Server, včetně doporučení týkajících se výkonu, najdete v tématu [SQL Server migrace databáze na Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o tom, jak bezpečně spravovat a sdílet klíče úložiště a signatury sdíleného přístupu, najdete v tématu [Azure Storage Průvodce zabezpečením](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
