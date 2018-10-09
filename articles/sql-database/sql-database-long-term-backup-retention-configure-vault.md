---
title: Konfigurace dlouhodobého uchovávání záloh – Azure SQL Database | Dokumentace Microsoftu
description: Naučte se, ukládáním automatizovaných záloh v trezoru služby Azure Recovery Services a obnovením z trezoru služby Azure Recovery Services
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 499689e4d36f368746e5e0ea9e6bc3f3bf0ec0d9
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868221"
---
# <a name="configure-long-term-backup-retention-using-azure-recovery-services-vault"></a>Konfigurace dlouhodobého uchovávání záloh pomocí trezoru služby Azure Recovery Services

Můžete nakonfigurovat trezor služeb zotavení Azure k ukládání záloh Azure SQL database a poté obnovit databáze pomocí záloh v trezoru pomocí webu Azure portal nebo Powershellu.

> [!NOTE]
> Jako součást počáteční verzi preview dlouhodobého uchovávání záloh v říjnu 2016 byly zálohy uložené v trezoru služby Azure Recovery Services. Tato aktualizace odebere tuto závislost, ale kvůli zpětné kompatibilitě se původní rozhraní API podporuje až do 31. května 2018. Pokud budete potřebovat pro interakci s zálohy v trezoru Azure Services Recovery, přečtěte si téma [dlouhodobého uchovávání záloh pomocí služby Azure Recovery Services vault](sql-database-long-term-backup-retention-configure-vault.md).

## <a name="azure-portal"></a>portál Azure

Následující části ukazují, jak pomocí webu Azure portal můžete nakonfigurovat trezoru služby Azure Recovery Services, zobrazení záloh v trezoru a obnovení z trezoru.

### <a name="configure-the-vault-register-the-server-and-select-databases"></a>Konfigurace trezoru, registraci serveru a výběr databází

Nakonfigurujete trezor služby Azure Recovery Services pro [uchovávání automatizovaných záloh](sql-database-long-term-retention.md) dobu delší, než je doba uchovávání vaší úrovně služby.

1. Otevřít **systému SQL Server** stránky pro váš server.

   ![stránku serveru SQL](./media/sql-database-get-started-portal/sql-server-blade.png)

2. Klikněte na **Dlouhodobé uchovávání záloh**.

   ![odkaz na dlouhodobé uchovávání záloh](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. Na **dlouhodobého uchovávání záloh** stránky pro váš server, přečtěte si a přijměte podmínky verze preview (Pokud budete mít již neudělali nebo pokud tato funkce už je ve verzi preview).

   ![přijetí podmínek verze preview](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Pokud chcete nakonfigurovat dlouhodobé uchovávání záloh, vyberte databázi v mřížce a potom klikněte na tlačítko **konfigurovat** na panelu nástrojů.

   ![výběr databáze pro dlouhodobé uchovávání záloh](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. Na **konfigurovat** klikněte na **konfigurovat požadované nastavení** pod **trezor služby Recovery Services**.

   ![odkaz na konfiguraci trezoru](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. Na **trezor služby Recovery services** vyberte existující trezor, pokud existuje. Pokud se pro vaše předplatné nenašel žádný trezor služby Recovery Services, kliknutím proces ukončete a vytvořte trezor služby Recovery Services.

   ![Vytvoření trezoru odkazu](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. Na **trezory služby Recovery Services** klikněte na **přidat**.

   ![Přidání odkazu na úložiště](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)

8. Na **trezor služby Recovery Services** stránky, zadejte platný název pro trezor služby Recovery Services.

   ![název nového trezoru](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Vyberte svoje předplatné a skupinu prostředků a potom vyberte umístění trezoru. Až budete hotovi, klikněte na **Vytvořit**.

   ![Vytvoření trezoru](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > Trezor musí být umístěn ve stejné oblasti Azure jako logický server SQL Azure a musí používat stejnou skupinu prostředků jako logický server.

10. Po vytvoření nového trezoru proveďte nezbytné kroky se vrátíte **trezor služby Recovery services** stránky.

11. Na **trezor služby Recovery services** stránky, klikněte na trezor a klikněte na **vyberte**.

   ![výběr existujícího trezoru](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. Na **konfigurovat** stránky, zadejte platný název pro nové zásady uchovávání informací, úpravy výchozích zásad uchovávání informací podle potřeby a potom klikněte na tlačítko **OK**.

   ![definování zásady uchovávání informací](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

   > [!NOTE]
   > Názvy zásad uchovávání informací Nepovolit některé znaků včetně mezer.

13. Na **dlouhodobého uchovávání záloh** stránce pro vaši databázi, klikněte na tlačítko **Uložit** a potom klikněte na tlačítko **OK** použít zásadu dlouhodobého uchovávání záloh na všechny vybrané databáze.

   ![definování zásady uchovávání informací](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Kliknutím na **Uložit** povolte nakonfigurované dlouhodobé uchovávání záloh v trezoru služby Azure Recovery Services pomocí této nové zásady.

   ![definování zásady uchovávání informací](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

> [!IMPORTANT]
> Po nakonfigurování se zálohy v trezoru objeví během příštích sedm dnů. Nepokračujte v tomto kurzu, dokud se zálohy neobjeví v trezoru.

### <a name="view-backups-in-long-term-retention-using-azure-portal"></a>Zobrazení záloh v rámci dlouhodobého uchovávání pomocí webu Azure portal

Zobrazit informace o zálohách databáze v [dlouhodobého uchovávání záloh](sql-database-long-term-retention.md).

1. Na webu Azure Portal, otevřete trezoru služby Azure Recovery Services pro zálohování databáze (přejděte na **všechny prostředky** a vyberte ho ze seznamu prostředků pro vaše předplatné) Chcete-li zobrazit velikost úložiště využitá službou vaše zálohy databáze v trezor.

   ![zobrazení trezoru služby recovery services se zálohami](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Otevřít **SQL database** stránce pro vaši databázi.

   ![novou stránku s ukázkou db](./media/sql-database-get-started-portal/new-sample-db-blade.png)

3. Na panelu nástrojů klikněte na **Obnovit**.

   ![panel nástrojů – obnovit](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. Klikněte na stránce obnovení **dlouhodobé**.

5. V části Zálohy v trezoru Azure klikněte na **Zvolit zálohu** a zobrazte dostupné zálohy databáze v rámci dlouhodobého uchovávání záloh.

   ![zálohy v trezoru](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention-using-the-azure-portal"></a>Obnovte databázi ze zálohy v dlouhodobém uchovávání záloh pomocí webu Azure portal

Obnovit databázi do nové databáze ze zálohy v trezoru služby Azure Recovery Services.

1. Na **zálohy v trezoru Azure** klikněte na zálohování a obnovení a pak klikněte na tlačítko **vyberte**.

   ![výběr zálohy v trezoru](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. Do textového pole **Název databáze** zadejte název obnovené databáze.

   ![nový název databáze](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Kliknutím na **OK** obnovte databázi ze zálohy v trezoru do nové databáze.

4. Pokud chcete zobrazit stav úlohy obnovení, na panelu nástrojů klikněte na ikonu oznámení.

   ![průběh úlohy obnovení z trezoru](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Po dokončení úlohy obnovení otevřete **databází SQL** stránku a zobrazte nově obnovenou databázi.

   ![obnovená databáze z trezoru](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> Odtud se můžete pomocí aplikace SQL Server Management Studio připojit k obnovené databázi a provádět požadované úlohy, jako je například [extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze na název existující databáze](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="powershell"></a>PowerShell

Následující části ukazují, jak pomocí prostředí PowerShell můžete nakonfigurovat trezoru služby Azure Recovery Services, zobrazení záloh v trezoru a obnovení z trezoru.

### <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

Použití [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) vytvoření trezoru služby recovery services.

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

### <a name="set-your-server-to-use-the-recovery-vault-for-its-long-term-retention-backups"></a>Nastavení serveru na použití trezoru obnovení pro dlouhodobě uchovávané zálohy

Použití [Set-AzureRmSqlServerBackupLongTermRetentionVault](/powershell/module/azurerm.sql/set-azurermsqlserverbackuplongtermretentionvault) k přidružení trezoru dříve vytvořeného recovery services s konkrétním serverem Azure SQL.

```PowerShell
# Set your server to use the vault to for long-term backup retention

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Vytvoření zásady uchovávání informací

Zásada uchovávání informací stanoví, kde můžete nastavit dobu uchování zálohy databáze. Použít [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject) rutiny pro získání výchozí zásady uchovávání informací pro použití jako šablony pro vytváření zásad. V této šabloně je doba uchování nastavena na 2 roky. V dalším kroku spusťte [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy) nakonec vytvořte zásadu.

> [!NOTE]
> Některé rutiny vyžadují nastavit kontext trezoru před spuštěním ([Set-AzureRmRecoveryServicesVaultContext](/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)), zobrazí se tato rutina v několika souvisejících fragmentech kódu. Můžete nastavit kontext, protože zásada je součástí trezoru. Můžete vytvořit více zásad uchovávání pro každý trezor a pak použít požadovanou zásadu u konkrétních databází.

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

Použití [Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabasebackuplongtermretentionpolicy) rutiny použijí nové zásady u konkrétní databáze.

```PowerShell
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

### <a name="view-backup-info-and-backups-in-long-term-retention"></a>Zobrazení informací o záloze a záloh v rámci dlouhodobého uchovávání

Zobrazit informace o zálohách databáze v [dlouhodobého uchovávání záloh](sql-database-long-term-retention.md).

Chcete-li zobrazit informace o zálohování pomocí následujících rutin:

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

Obnovení z dlouhodobě uchovávaných záloh používá [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) rutiny.

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
> Odtud se můžete připojit k obnovené databázi pomocí SQL Server Management Studio a provádět požadované úlohy, například za účelem extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovenou databáze na název existující databáze. Zobrazit [bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="how-to-cleanup-backups-in-recovery-services-vault"></a>Postup vyčištění záloh v trezoru služby Recovery Services

Od 1. července 2018 se už nepoužívá rozhraní API V1 zleva doprava a všechny existující zálohy v trezorech služby Recovery migroval do úložiště kontejnerů LTR spravovaných službou SQL Database. Pokud chcete mít jistotu, že se vám už nebude účtovat původní zálohy, že byly odebrány z tyto trezory po migraci. Ale pokud jste umístili zámek pro váš trezor záloh zůstane. Chcete-li se vyhnout zbytečným poplatkům, můžete ručně odebrat starší zálohy z trezor služby Recovery Services pomocí následujícího skriptu.

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
