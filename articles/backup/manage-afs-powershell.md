---
title: Správa záloh sdílení souborů Azure pomocí PowerShellu
description: Zjistěte, jak pomocí PowerShellu spravovat a monitorovat sdílené složky Azure zálohované službou Azure Backup.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a9dc421db740963fc5cd11e868eb383694376ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083179"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Správa záloh sdílení souborů Azure pomocí PowerShellu

Tento článek popisuje, jak používat Azure PowerShell ke správě a monitorování sdílených složek Azure, které jsou zálohovány službou Azure Backup.

> [!WARNING]
> Ujistěte se, že verze PS je upgradována na minimální verzi pro "Az.RecoveryServices 2.6.0" pro zálohy AFS. Další podrobnosti naleznete [v části](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) popisující požadavek na tuto změnu.

## <a name="modify-the-protection-policy"></a>Změna zásad ochrany

Chcete-li změnit zásady používané pro zálohování sdílené složky Azure, použijte [enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Zadejte příslušnou položku zálohování a nové zásady zálohování.

Následující příklad změní zásady ochrany **testAzureFS** z **dailyafs** na **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Sledování úloh zálohování a obnovení

Operace zálohování a obnovení na vyžádání vrátí úlohu spolu s ID, jak je znázorněno při [spuštění zálohy na vyžádání](backup-azure-afs-automation.md#trigger-an-on-demand-backup). Pomocí rutiny [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) můžete sledovat průběh úlohy a podrobnosti.

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

* Zastavit všechny budoucí úlohy zálohování a *odstranit* všechny body obnovení
* Zastavit všechny budoucí úlohy zálohování, ale *ponechat* body obnovení

Může být náklady spojené s opuštěním body obnovení v úložišti, jako základní snímky vytvořené službou Azure Backup zůstanou zachovány. Výhodou opuštění bodů obnovení je však obnovení sdílené složky později, pokud je to žádoucí. Informace o nákladech na opuštění bodů obnovení naleznete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/storage/files/). Pokud se rozhodnete odstranit všechny body obnovení, nemůžete sdílenou složku obnovit.

## <a name="stop-protection-and-retain-recovery-points"></a>Zastavit ochranu a zachovat body obnovení

Chcete-li ochranu zastavit při uchovávání dat, použijte rutinu [Disable-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0)

Následující příklad zastaví ochranu sdílené složky *afsfileshare,* ale zachová všechny body obnovení:

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

Atribut ID úlohy ve výstupu odpovídá ID úlohy, která je vytvořena službou zálohování pro operaci "stop protection". Chcete-li sledovat stav úlohy, použijte rutinu [Get-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0)

## <a name="stop-protection-without-retaining-recovery-points"></a>Zastavení ochrany bez zachování bodů obnovení

Chcete-li ochranu zastavit bez zachování bodů obnovení, použijte rutinu [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) a přidejte parametr **-RemoveRecoveryPoints.**

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

[Přečtěte si o](manage-afs-backup.md) správě záloh sdílení souborů Azure na webu Azure Portal.
