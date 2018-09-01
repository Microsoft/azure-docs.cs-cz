---
title: Obnovení služby Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Jak do Průvodce pro obnovení Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/29/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 31b137cca55b1dd249368ba5e287496582152c9f
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382656"
---
# <a name="restoring-azure-sql-data-warehouse"></a>Obnovení Azure SQL Data Warehouse 
V tomto článku se dozvíte, jak provést následující kroky:

- Vytvoření bodu obnovení
- Obnovení z automatického obnovení bodu nebo bodu obnovení definovaný uživatelem
- Obnovit z odstraněné databáze
- Obnovit z geografické zálohy
- Vytvoření kopie vašeho datového skladu z bodu obnovení definovaný uživatelem

> [!NOTE]
> Od 8/27 mezi servery obnovení byl zakázán z důvodu známého regrese. Aktivně pracujeme na opravě v naší nejvyšší prioritou. Omlouváme se za nepříjemnosti. Do té doby můžete využít vaše [geografické zálohování](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore#restore-from-an-azure-geographical-region) obnovit napříč servery.  
>

## <a name="before-you-begin"></a>Než začnete
**Ověřte kapacitu jednotek DTU.** Každý datový sklad SQL je hostitelem SQL serveru (např. myserver.database.windows.net), který má výchozí kvóty DTU.  Předtím, než bude možné obnovit datový sklad SQL, ověřte, že serveru SQL server má dostatek zbývající kvóta DTU databáze obnovena. Pokud chcete zjistit, jak chcete-li vypočítat potřebné DTU, nebo požádat o další DTU, přečtěte si téma [žádost o změnu kvóty DTU][Request a DTU quota change].

# <a name="restore-through-powershell"></a>Obnovit přes PowerShell

## <a name="install-powershell"></a>Instalace PowerShellu
Chcete-li používat Azure PowerShell s využitím SQL Data Warehouse, je potřeba nainstalovat Azure PowerShell verze 1.0 nebo vyšší.  Verzi zjistíte spuštěním **Get-Module - ListAvailable-Name AzureRM**.  Nejnovější verzi si můžete nainstalovat z [instalačního programu webové platformy Microsoft][Microsoft Web Platform Installer].  Další informace o instalaci nejnovější verze najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database"></a>Obnovit databázi aktivní nebo pozastavena
Chcete-li obnovit databázi před použitím bodu obnovení [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] rutiny Powershellu.

1. Otevřete Windows PowerShell.

2. Připojte se ke svému účtu Azure a vypsat všechna předplatná spojená s vaším účtem.

3. Vyberte předplatné, která obsahuje databázi obnovit.

4. Vypsat body obnovení pro databázi.

5. Vyberte bod obnovení požadovaných pomocí RestorePointCreationDate.

   > [!NOTE]
   > Při obnovení, můžete zadat různé ServiceObjectiveName (DWU) nebo jiný server, které se nacházejí v jiné oblasti.

6. Obnovení databáze do místa požadovaného obnovení.

7. Ověřte, že obnovené databáze online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Po dokončení obnovení můžete nakonfigurovat obnovené databáze pomocí následujících [nakonfigurovat svou databázi po obnovení][Configure your database after recovery].
>

## <a name="copy-your-data-warehouse-with-user-defined-restore-points"></a>Zkopírujte váš datový sklad s body obnovení definovaný uživatelem
Obnovení databáze před použitím bodu obnovení uživatelem definované [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] rutiny Powershellu.

1. Otevřete Windows PowerShell.
2. Připojte se ke svému účtu Azure a vypsat všechna předplatná spojená s vaším účtem.
3. Vyberte předplatné, která obsahuje databázi obnovit.
4. Vytvořit bod obnovení pro okamžité kopírování databáze
5. Přejmenujte databáze na nějaký dočasný název.
5. Načtěte nejnovější bod obnovení pomocí zadaného RestorePointLabel.
6. Získejte id prostředku databáze, kterou chcete spustit obnovení
6. Obnovení databáze do místa požadovaného obnovení.
7. Ověřte, že obnovené databáze online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$TempDatabaseName = "<YourTemporaryDatabaseName>"
$Label = "<YourRestorePointLabel"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzureRmSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

# Rename the database to a temporary name
Set-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -NewName $TempDatabaseName

# Get the most recent restore point with the specified label
$LabelledRestorePoint = Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName | where {$_.RestorePointLabel -eq $Label} | sort {$_.RestorePointCreationDate} | select -Last 1

# Get the resource id of the database
$ResourceId = (Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName).ResourceId

# Restore the database to its original name from the labelled restore point from the temporary database
$RestoredDatabase = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ResourceId $ResourceId -PointInTime $LabelledRestorePoint.RestorePointCreationDate -TargetDatabaseName $DatabaseName

# Verify the status of restored database
$RestoredDatabase.status

# The original temporary database can be deleted at this point

```

## <a name="restore-a-deleted-database"></a>Obnovení odstraněné databáze
Chcete-li obnovit odstraněnou databázi, použijte [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] rutiny.

1. Otevřete Windows PowerShell.
2. Připojte se ke svému účtu Azure a vypsat všechna předplatná spojená s vaším účtem.
3. Vyberte předplatné, které obsahuje odstraněnou databázi obnovit.
4. Získání konkrétní odstraněnou databázi.
5. Obnovení odstraněné databáze.
6. Ověřte, že obnovené databáze online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Po dokončení obnovení můžete nakonfigurovat obnovené databáze pomocí následujících [nakonfigurovat svou databázi po obnovení][Configure your database after recovery].
>

## <a name="restore-from-an-azure-geographical-region"></a>Obnovení z Azure geografické oblasti
Chcete-li obnovit databázi, použijte [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] rutiny.

> [!NOTE]
> Můžete provést geografické obnovení na Gen2! Uděláte to tak, zadejte Gen2 ServiceObjectiveName (třeba DW1000**c**) jako volitelný parametr.
>

1. Otevřete Windows PowerShell.
2. Připojte se ke svému účtu Azure a vypsat všechna předplatná spojená s vaším účtem.
3. Vyberte předplatné, která obsahuje databázi obnovit.
4. Získáte databáze, kterou chcete obnovit.
5. Vytvořte žádost o obnovení pro databázi.
6. Ověřte stav databázi geograficky obnovit.

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Po dokončení obnovení konfigurace vaší databáze, najdete v článku [nakonfigurovat svou databázi po obnovení][Configure your database after recovery].
>

Pokud zdrojová databáze je povolené šifrování TDE, bude obnovenou databázi povoleno TDE.

# <a name="restore-through-the-azure-portal"></a>Obnovit na webu Azure Portal

## <a name="create-a-user-defined-restore-point"></a>Vytvořit bod obnovení definovaný uživatelem
1. Přihlaste se na web [Azure Portal][Azure portal].

2. Přejděte do SQL data warehouse, kterou chcete vytvořit bod obnovení pro.

3. V horní části okna Přehled, vyberte **+ nový bod obnovení**.

    ![Nový bod obnovení](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)
    
4. Zadejte název pro bod obnovení.

    ![Názvem bodu obnovení](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database"></a>Obnovit databázi aktivní nebo pozastavena
1. Přihlaste se na web [Azure Portal][Azure portal].
2. Přejděte do SQL data warehouse, kterou chcete obnovit z.
3. V horní části okna Přehled, vyberte **obnovení**.

    ![ Obnovení – přehled](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)
    
4. Vyberte buď **body obnovení automaticky** nebo **uživatelem definované body obnovení**.

    ![Body obnovení automaticky](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)
    
5. Pro body obnovení uživatelsky definovaná **vyberte bod obnovení** nebo **vytvořit nový bod obnovení uživatelem definované**.

    ![Uživatelem definované body obnovení](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-a-deleted-database"></a>Obnovení odstraněné databáze
1. Přihlaste se na web [Azure Portal][Azure portal].
2. Přejděte na server SQL, který byl hostitelem odstraněnou databázi.
3. Vyberte ikonu odstraněné databáze v obsahu.

    ![Odstraněné databáze](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_0.png)
    
4. Vyberte, kterou chcete obnovit odstraněnou databázi.

    ![Vyberte odstraněné databáze](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_1.png)
    
5. Zadejte nový název databáze.

    ![Zadejte název databáze](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_2.png)

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
