---
title: Konfigurace dlouhodobé uchovávání záloh – Azure SQL Database | Microsoft Docs
description: Zjistěte, jak uložit automatizované zálohování do trezoru služeb zotavení Azure a obnovit z trezoru služeb zotavení Azure
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 05/08/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: feefe68fbe6681ee4b450503606ac8c4f25d5a39
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130256"
---
# <a name="configure-and-restore-from-azure-sql-database-long-term-backup-retention-using-azure-recovery-services-vault"></a>Konfigurace a obnovení z Azure SQL Database dlouhodobé zálohování uchovávání pomocí trezoru služeb zotavení Azure

Můžete nakonfigurovat na trezor služeb zotavení Azure uložte zálohy databáze Azure SQL a poté obnovte databázi pomocí zálohy uchovávány v úložišti pomocí portálu Azure nebo Powershellu.

> [!NOTE]
> Jako součást počáteční verze Preview dlouhodobé uchovávání záloh v říjnu 2016 byly zálohy uložené ve službě Azure Recovery Services trezoru. Tato aktualizace odebere tuto závislost, ale kvůli zpětné kompatibilitě se původní rozhraní API podporuje do 31 může 2018. Pokud potřebujete pro interakci s záloh v trezoru Azure Services Recovery, přečtěte si [dlouhodobé uchovávání záloh pomocí trezoru službu Azure Recovery Services](sql-database-long-term-backup-retention-configure-vault.md). 


## <a name="azure-portal"></a>Azure Portal

Následující části ukazují, jak pomocí portálu Azure můžete nakonfigurovat trezor služeb zotavení Azure, Zobrazit zálohy v trezoru a obnovení z trezoru.

### <a name="configure-the-vault-register-the-server-and-select-databases"></a>Konfigurace trezoru, registraci serveru a vyberte databáze

Konfigurace trezoru služeb zotavení Azure [zachovat automatizované zálohování](sql-database-long-term-retention.md) po dobu delší než doba uchování pro vaše vrstvu služeb. 

1. Otevřete **systému SQL Server** stránka serveru.

   ![stránka serveru SQL](./media/sql-database-get-started-portal/sql-server-blade.png)

2. Klikněte na **Dlouhodobé uchovávání záloh**.

   ![odkaz na dlouhodobé uchovávání záloh](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. Na **dlouhodobé uchovávání záloh** stránky pro váš server, přečtěte si a přijměte podmínky verze preview (Pokud budete mít neudělali - nebo tato funkce je už ve verzi preview).

   ![přijetí podmínek verze preview](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Konfigurace dlouhodobé uchovávání záloh, vyberte tuto databázi v mřížce a pak klikněte na tlačítko **konfigurace** na panelu nástrojů.

   ![výběr databáze pro dlouhodobé uchovávání záloh](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. Na **konfigurace** klikněte na tlačítko **konfigurovat požadované nastavení** pod **trezoru služby zotavení**.

   ![odkaz na konfiguraci trezoru](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. Na **trezoru služeb zotavení** vyberte existující trezor, pokud existuje. Pokud se pro vaše předplatné nenašel žádný trezor služby Recovery Services, kliknutím proces ukončete a vytvořte trezor služby Recovery Services.

   ![Vytvoření trezoru propojení](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. Na **trezory služeb zotavení** klikněte na tlačítko **přidat**.

   ![Přidat odkaz na trezoru](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. Na **trezor služeb zotavení** stránky, zadejte platný název pro trezor služeb zotavení.

   ![název nového trezoru](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Vyberte svoje předplatné a skupinu prostředků a potom vyberte umístění trezoru. Až budete hotovi, klikněte na **Vytvořit**.

   ![Vytvoření trezoru](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > Trezor musí být umístěn ve stejné oblasti Azure jako logický server SQL Azure a musí používat stejnou skupinu prostředků jako logický server.
   >

10. Po vytvoření nového trezoru provést nezbytné kroky se vrátíte do **trezoru služeb zotavení** stránky.

11. Na **trezoru služeb zotavení** , kliknutím na trezor a pak klikněte na tlačítko **vyberte**.

   ![výběr existujícího trezoru](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. Na **konfigurace** stránky, zadejte platný název pro nové zásady uchovávání informací, úpravy výchozích zásad uchovávání informací podle potřeby a pak klikněte na tlačítko **OK**.

   ![definování zásady uchovávání informací](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)
   
   >[!NOTE]
   >Názvy zásad uchovávání Nepovolit některé znaků včetně mezer.

13. Na **dlouhodobé uchovávání záloh** stránky pro vaši databázi, klikněte na tlačítko **Uložit** a pak klikněte na **OK** uplatňovat zásady dlouhodobé uchovávání záloh na všech vybraných databázích.

   ![definování zásady uchovávání informací](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Kliknutím na **Uložit** povolte nakonfigurované dlouhodobé uchovávání záloh v trezoru služby Azure Recovery Services pomocí této nové zásady.

   ![definování zásady uchovávání informací](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

> [!IMPORTANT]
> Po nakonfigurování se zálohy v trezoru objeví během příštích sedm dnů. Nepokračujte v tomto kurzu, dokud se zálohy neobjeví v trezoru.
>

### <a name="view-backups-in-long-term-retention-using-azure-portal"></a>Zobrazení záloh v dlouhodobé uchovávání pomocí portálu Azure

Zobrazit informace o zálohování databáze v [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md). 

1. Na portálu Azure otevřete svůj trezor služeb zotavení Azure pro zálohování databáze (přejděte na **všechny prostředky** a vyberte ji ze seznamu prostředků pro vaše předplatné) zobrazíte velikost úložiště používané v zálohování databáze trezor.

   ![zobrazení trezoru služby recovery services se zálohami](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Otevřete **databáze SQL** stránky pro vaši databázi.

   ![Nová stránka ukázkové databáze](./media/sql-database-get-started-portal/new-sample-db-blade.png)

3. Na panelu nástrojů klikněte na **Obnovit**.

   ![panel nástrojů – obnovit](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. Na stránce obnovení klikněte na **dlouhodobé**.

5. V části Zálohy v trezoru Azure klikněte na **Zvolit zálohu** a zobrazte dostupné zálohy databáze v rámci dlouhodobého uchovávání záloh.

   ![zálohy v trezoru](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention-using-the-azure-portal"></a>Obnovte databázi ze zálohy v dlouhodobé uchovávání záloh pomocí portálu Azure

Obnovení databáze pro novou databázi ze zálohy v trezoru služeb zotavení Azure.

1. Na **Azure trezoru záloh** stránky, klikněte na zálohu k obnovení a pak klikněte na tlačítko **vyberte**.

   ![výběr zálohy v trezoru](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. Do textového pole **Název databáze** zadejte název obnovené databáze.

   ![nový název databáze](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Kliknutím na **OK** obnovte databázi ze zálohy v trezoru do nové databáze.

4. Pokud chcete zobrazit stav úlohy obnovení, na panelu nástrojů klikněte na ikonu oznámení.

   ![průběh úlohy obnovení z trezoru](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Po dokončení úlohy obnovení, otevřete **databází SQL** zobrazit nově obnovenou databázi.

   ![obnovená databáze z trezoru](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> Odtud se můžete pomocí aplikace SQL Server Management Studio připojit k obnovené databázi a provádět požadované úlohy, jako je například [extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze na název existující databáze](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="powershell"></a>PowerShell

Následující části ukazují, jak pomocí prostředí PowerShell nakonfigurovat trezor služeb zotavení Azure, zobrazení v trezoru a obnovení z trezoru záloh.

### <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

Použití [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) k vytvoření trezoru služeb zotavení.

> [!IMPORTANT]
> Trezor musí být umístěn ve stejné oblasti Azure jako logický server SQL Azure a musí používat stejnou skupinu prostředků jako logický server.

```PowerShell
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-for-its-long-term-retention-backups"></a>Nastavení serveru na použití obnovení trezoru pro jeho dlouhodobé uchovávání záloh

Použití [Set-AzureRmSqlServerBackupLongTermRetentionVault](/powershell/module/azurerm.sql/set-azurermsqlserverbackuplongtermretentionvault) rutiny přidružit trezoru služeb zotavení dříve vytvořenou pro konkrétní server Azure SQL.

```PowerShell
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Vytvoření zásady uchovávání informací

Zásada uchovávání informací stanoví, kde můžete nastavit dobu uchování zálohy databáze. Použít [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) rutiny výchozí zásady uchovávání informací použít jako šablonu pro vytváření zásad. V této šabloně je nastavit dobu uchování na 2 roky. Potom spustíte [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy) nakonec vytvoření zásad. 

> [!NOTE]
> Některé rutiny vyžadují, abyste nastavili trezoru rámci dřív, než spustíte ([Set-AzureRmRecoveryServicesVaultContext](/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)) tak, aby zobrazila tato rutina v několika související fragmenty kódu. Můžete nastavit kontext, protože zásady je součástí trezoru. Můžete vytvořit více zásad uchovávání pro každý trezor a pak použít požadovanou zásadu u konkrétních databází. 


```PowerShell
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>Konfigurace databáze na používání dříve definované zásady uchovávání

Použití [Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabasebackuplongtermretentionpolicy) rutiny použijí nové zásady k určité databázi.

```PowerShell
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

### <a name="view-backup-info-and-backups-in-long-term-retention"></a>Zobrazení informací o záloze a záloh v rámci dlouhodobého uchovávání

Zobrazit informace o zálohování databáze v [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md). 

Chcete-li zobrazit informace o zálohování pomocí následující rutiny:

- [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)

```PowerShell
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore

# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Obnovení databáze ze zálohy v rámci dlouhodobého uchovávání záloh

Obnovení ze zálohy dlouhodobé uchovávání používá [obnovení-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) rutiny.

```PowerShell
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```


> [!NOTE]
> Tady můžete připojit k obnovené databázi pomocí SQL Server Management Studio potřebné úkoly, například za účelem extrahování bit dat z obnovené databáze chcete zkopírovat do existující databáze nebo odstranit existující databázi a přejmenujte obnovenou databáze na název existující databáze. V tématu [bodu v době obnovení](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="how-to-cleanup-backups-in-recovery-services-vault"></a>Postup vyčištění záloh v trezoru služeb zotavení

Od verze 1. července 2018 je zastaralá rozhraní API V1 zleva doprava a do kontejnerů úložiště LTR spravuje databáze SQL se migrovaly všechny existující zálohy v trezory služby zotavení. K zajištění, že jste už účtovat pro původní zálohy, budou odebrána z trezory po migraci. Ale pokud jste umístili zámek pro váš trezoru zálohování bude stále existuje. Aby se zabránilo zbytečným poplatky, můžete ručně odebrat starší zálohy z trezoru služby zotavení pomocí následujícího skriptu. 

```PowerShell
<#
.EXAMPLE
    .\Drop-LtrV1Backup.ps1 -SubscriptionId “{vault_sub_id}” -ResourceGroup “{vault_resource_group}” -VaultName “{vault_name}” 
#>
[CmdletBinding()]
Param (
    [Parameter(Mandatory = $true, HelpMessage="The vault subscription ID")]
    $SubscriptionId,

    [Parameter(Mandatory = $true, HelpMessage="The vault resource group name")]
    $ResourceGroup,

    [Parameter(Mandatory = $true, HelpMessage="The vault name")]
    $VaultName
)

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $SubscriptionId

$vaults = Get-AzureRmRecoveryServicesVault
$vault = $vaults | where { $_.Name -eq $VaultName }

Set-AzureRmRecoveryServicesVaultContext -Vault $vault

$containers = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL

ForEach ($container in $containers)
{
   $canDeleteContainer = $true  
   $ItemCount = 0
   Write-Host "Working on container" $container.Name
   $items = Get-AzureRmRecoveryServicesBackupItem -container $container -WorkloadType AzureSQLDatabase
   ForEach ($item in $items)
   {
          write-host "Deleting item" $item.name
          Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints -item $item -Force
   }

   Write-Host "Deleting container" $container.Name
   Unregister-AzureRmRecoveryServicesBackupContainer -Container $container
}
```

## <a name="next-steps"></a>Další postup

- Další informace o automatických zálohách generovaných službou najdete u popisu [automatických záloh](sql-database-automated-backups.md).
- Další informace o dlouhodobém uchovávání záloh najdete v části [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md)
- Další informace o obnovování ze záloh najdete v části [obnovení ze zálohy](sql-database-recovery-using-backups.md)
