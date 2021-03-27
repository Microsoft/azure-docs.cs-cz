---
title: Zálohování Azure Managed Disks pomocí Azure PowerShell
description: Naučte se, jak zálohovat Azure Managed Disks pomocí Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2e286128185c1ac7fe4861a9b06de52e10d4139a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630400"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Zálohování Azure Managed Disks pomocí Azure PowerShell

Tento článek vysvětluje, jak pomocí Azure PowerShell zálohovat službu [Azure Managed disks](../virtual-machines/managed-disks-overview.md) .

V tomto článku se naučíte:

- Vytvoření trezoru záloh

- Vytvoření zásad zálohování

- Konfigurace zálohy disku Azure

- Spuštění úlohy zálohování na vyžádání

Informace o dostupnosti oblasti Azure disk Backup, podporovaných scénářích a omezeních najdete v tématu věnovaném [maticům podpory](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Vytvoření trezoru záloh

Trezor záloh je entita úložiště v Azure, která uchovává data pro různé novější úlohy, které Azure Backup podporuje, například servery Azure Database for PostgreSQL a disky Azure. Trezory služby Backup usnadňují uspořádání zálohovaných dat a současně minimalizují nároky na správu. Trezory služby Backup jsou založené na modelu Azure Resource Manager Azure, který poskytuje vylepšené možnosti, které usnadňují zabezpečení zálohovaných dat.

Před vytvořením trezoru záloh vyberte redundanci úložiště dat v rámci trezoru. Pak pokračujte v vytváření trezoru záloh s touto redundancí úložiště a umístěním. V tomto článku vytvoříme úložiště záloh "TestBkpVault" v oblasti "westus" pod skupinou prostředků "testBkpVaultRG". K vytvoření trezoru záloh použijte příkaz [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) . Přečtěte si další informace o [Vytvoření trezoru záloh](./backup-vault-overview.md#create-a-backup-vault).

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

Po vytvoření trezoru vytvoříme zásadu zálohování pro ochranu disků Azure.

## <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Pro pochopení vnitřních komponent zásad zálohování pro zálohování disku Azure načtěte šablonu zásad pomocí příkazu [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true). Tento příkaz vrátí šablonu výchozí zásady pro daný typ zdroje dat. Pomocí této šablony zásad můžete vytvořit novou zásadu.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Šablona zásad se skládá z triggeru (který určuje, co spustí zálohování) a životního cyklu (který určuje, kdy se má záloha odstranit/zkopírovat nebo přesunout). Ve službě Azure disk Backup je výchozí hodnotou triggeru naplánovaná hodinová aktivace každých 4 hodin (PT4H) a uchování každé zálohy 7 dní.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Azure disk Backup nabízí více záloh za den. Pokud potřebujete častější zálohování, vyberte **hodinovou** četnost zálohování s možností provádět zálohování s intervaly každých 4, 6, 8 nebo 12 hodin. Zálohy jsou plánovány na základě zvoleného **časového** intervalu. Pokud například vyberete **každou 4 hodiny**, zálohování se povede přibližně v intervalu každých 4 hodin, aby se zálohy rovnoměrně rozdělují mezi jednotlivé dny. Pokud je zálohování jednou denně dostatečné, zvolte **denní** četnost zálohování. V denní četnosti zálohování můžete zadat čas, kdy se mají zálohy vymezit. Je důležité si uvědomit, že čas dne označuje čas zahájení zálohování a ne čas, kdy se zálohování dokončí. Čas potřebný k dokončení operace zálohování závisí na různých faktorech, včetně velikosti disku, a rychlosti přenosů mezi po sobě jdoucími zálohami. Zálohování disku Azure je ale zálohování bez agentů, které používá [přírůstkové snímky](../virtual-machines/disks-incremental-snapshots.md), což nemá vliv na výkon produkčních aplikací.

   >[!NOTE]
   > I když vybraný trezor může mít nastavení globální redundance, služba Azure disk Backup aktuálně podporuje pouze úložiště snímků. Všechny zálohy se ukládají do skupiny prostředků ve vašem předplatném a nekopírují se do úložiště trezoru služby Backup.

Pokud chcete získat další informace o vytváření zásad, přečtěte si dokument [zásady zálohování disku Azure](backup-managed-disks.md#create-backup-policy) .

Pokud chcete upravit hodinovou frekvenci nebo dobu uchování, použijte příkazy [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) a/nebo [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) . Jakmile objekt zásad obsahuje všechny požadované hodnoty, pokračujte v vytváření nové zásady z objektu zásad pomocí [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>Konfigurace zálohování

Po vytvoření trezoru a zásad existují 3 kritické body, které uživatel musí zvážit při ochraně disku Azure.

### <a name="key-entities-involved"></a>Zahrnuté klíčové entity

#### <a name="disk-to-be-protected"></a>Disk, který se má chránit

Načte ID ARM disku, který se má chránit. Tato akce bude sloužit jako identifikátor disku. V rámci jiného předplatného budeme používat příklad disku s názvem "PSTestDisk" v rámci skupiny prostředků "diskrg".

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>Skupina prostředků snímku

Snímky disků se ukládají do skupiny prostředků v rámci vašeho předplatného. Jako vodítko doporučujeme vytvořit vyhrazenou skupinu prostředků jako úložiště dat snímku, které bude používat služba Azure Backup. Pokud máte vyhrazenou skupinu prostředků, můžete omezit přístupová oprávnění ke skupině prostředků, což poskytuje zabezpečení a snadné správy zálohovaných dat. Poznamenejte si ID ARM pro skupinu prostředků, do které chcete umístit snímky disků.

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Trezor služby Backup

Trezory služby Backup vyžadují oprávnění na disku a skupině prostředků snímku, aby bylo možné spouštět snímky a spravovat jejich životní cyklus. Pro přiřazení takových oprávnění se používá spravovaná identita trezoru přiřazená systémem. Pomocí příkazu [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) povolte spravovanou identitu přiřazenou systémem pro trezor služby Recovery Services.

### <a name="assign-permissions"></a>Přiřazení oprávnění

Uživatel musí přiřadit několik oprávnění přes RBAC do trezoru (reprezentovaného pomocí MSI MSI) a relevantního disku a/nebo disku RG. Tyto kroky můžete provádět prostřednictvím portálu nebo PowerShellu. Všechna související oprávnění jsou podrobně popsána v bodech 1, 2, 3 v [této části](backup-managed-disks.md#configure-backup).

### <a name="prepare-the-request"></a>Příprava žádosti

Po nastavení všech relevantních oprávnění se konfigurace zálohování provádí ve dvou krocích. Nejdřív připravujeme příslušný požadavek pomocí odpovídajícího trezoru, zásad, disku a snímku skupiny prostředků pomocí příkazu [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) . Pak odešleme požadavek na ochranu disku pomocí příkazu [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>Spuštění zálohování na vyžádání

Načtení relevantní instance zálohování, na které si uživatel přeje aktivovat zálohování pomocí [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true)

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Při aktivaci zálohování můžete zadat pravidlo uchovávání. Chcete-li zobrazit pravidla uchovávání v zásadách, přejděte k objektu zásad pro pravidla uchovávání. V níže uvedeném příkladu se zobrazí pravidlo s názvem Default a my použijeme toto pravidlo pro zálohování na vyžádání.

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Spusťte zálohování na vyžádání pomocí příkazu [Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>Sledování úloh

Sledujte všechny úlohy pomocí příkazu [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) . Můžete vypsat všechny úlohy a načíst konkrétní podrobnosti o úloze.

Pomocí AZ. ResourceGraph můžete také sledovat všechny úlohy ve všech úložištích služby Backup. Pomocí příkazu [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) Získejte relevantní úlohu, která může být v jakémkoli úložišti záloh.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Další kroky

- [Obnovení Azure Managed Disks pomocí Azure PowerShell](restore-managed-disks-ps.md)
