---
title: Správa záloh sdílených složek Azure pomocí PowerShellu
description: Naučte se používat PowerShell ke správě a monitorování sdílených složek Azure zálohovaných službou Azure Backup.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e2f07e56fb9a8715b1b53165ab5f4b45b4e20ccb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89000222"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Správa záloh sdílených složek Azure pomocí PowerShellu

Tento článek popisuje, jak pomocí Azure PowerShell spravovat a monitorovat sdílené složky Azure, které jsou zálohované službou Azure Backup.

> [!WARNING]
> Ujistěte se, že je verze prostředí PowerShell upgradována na minimální verzi příkazu AZ. RecoveryServices 2.6.0 pro zálohy na AFS. Další podrobnosti najdete [v části](backup-azure-afs-automation.md#important-notice-backup-item-identification) popisující požadavek na tuto změnu.

## <a name="modify-the-protection-policy"></a>Upravit zásady ochrany

Pokud chcete změnit zásady používané pro zálohování sdílené složky Azure, použijte [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Zadejte příslušnou položku zálohy a novou zásadu zálohování.

Následující příklad změní zásadu ochrany **testAzureFS** z **dailyafs** na **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Sledování úloh zálohování a obnovení

Operace zálohování a obnovení na vyžádání vrátí úlohu spolu s ID, jak je znázorněno [v případě spuštění zálohování na vyžádání](backup-azure-afs-automation.md#trigger-an-on-demand-backup). Pomocí rutiny [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) můžete sledovat průběh úloh a podrobnosti.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>Zastavení ochrany sdílené složky

Ochranu sdílených složek Azure můžete zastavit dvěma způsoby:

* Zastavit všechny budoucí úlohy zálohování a *Odstranit* všechny body obnovení
* Zastavte všechny budoucí úlohy zálohování, ale *ponechejte* body obnovení.

Je možné, že jsou k dispozici náklady spojené s ponecháním bodů obnovení v úložišti, protože jsou zachovány základní snímky vytvořené nástrojem Azure Backup. Výhodou ponechání bodů obnovení je však, že v případě potřeby můžete sdílenou složku obnovit později. Informace o nákladech na ponechávání bodů obnovení najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/storage/files/). Pokud se rozhodnete odstranit všechny body obnovení, sdílenou složku nemůžete obnovit.

## <a name="stop-protection-and-retain-recovery-points"></a>Zastavení ochrany a zachování bodů obnovení

Pokud chcete zastavit ochranu a zachovat data, použijte rutinu [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) .

Následující příklad zastaví ochranu sdílené složky *afsfileshare* , ale zachová všechny body obnovení:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

Atribut ID úlohy ve výstupu odpovídá ID úlohy, kterou vytvořila služba zálohování pro operaci zastavení ochrany. Ke sledování stavu úlohy použijte rutinu [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) .

## <a name="stop-protection-without-retaining-recovery-points"></a>Zastavit ochranu bez zachování bodů obnovení

Chcete-li zastavit ochranu bez uchování bodů obnovení, použijte rutinu [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) a přidejte parametr **-RemoveRecoveryPoints** .

Následující příklad zastaví ochranu sdílené složky *afsfileshare* bez zachování bodů obnovení:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>Další kroky

[Přečtěte si o](manage-afs-backup.md) správě záloh sdílených složek Azure v Azure Portal.
