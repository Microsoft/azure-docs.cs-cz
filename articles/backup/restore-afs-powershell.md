---
title: Obnovení souborů Azure pomocí PowerShellu
description: V tomto článku se dozvíte, jak obnovit soubory Azure pomocí služby Azure Backup a PowerShellu.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086822"
---
# <a name="restore-azure-files-with-powershell"></a>Obnovení souborů Azure pomocí PowerShellu

Tento článek vysvětluje, jak obnovit celou sdílenou složku nebo konkrétní soubory z bodu obnovení vytvořeného službou [Azure Backup](backup-overview.md) pomocí Azure Powershellu.

Můžete obnovit celou sdílenou složku nebo určité soubory ve sdílené složce. Můžete obnovit do původního umístění nebo do alternativního umístění.

> [!WARNING]
> Ujistěte se, že verze PS je upgradována na minimální verzi pro "Az.RecoveryServices 2.6.0" pro zálohy AFS. Další podrobnosti naleznete [v části](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) popisující požadavek na tuto změnu.

## <a name="fetch-recovery-points"></a>Načtení bodů obnovení

Pomocí [příkazu Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) zobrazíte seznam všech bodů obnovení zálohované položky.

V následujícím skriptu:

* Proměnná **$rp** je pole bodů obnovení pro vybranou položku zálohování za posledních sedm dní.
* Pole je seřazeno v obráceném pořadí času s nejnovějším bodem obnovení v indexu **0**.
* K výběru bodu obnovení použijte standardní indexování pole PowerShell.
* V příkladu **$rp[0]** vybere nejnovější bod obnovení.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Výstup je podobný následujícímu.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Po výběru příslušného bodu obnovení obnovíte sdílenou složku nebo soubor do původního umístění nebo do alternativního umístění.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Obnovení sdílené složky Azure do alternativního umístění

Pomocí [položky Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) obnovte vybraný bod obnovení. Zadejte tyto parametry k identifikaci alternativníumístění:

* **TargetStorageAccountName**: Účet úložiště, do kterého se obnoví zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **TargetFileShareName**: Sdílené složky v rámci účtu cílového úložiště, do kterého je obnoven zálohovaný obsah.
* **TargetFolder**: Složka pod sdílenou složkou souboru, do které jsou obnovena data. Pokud má být zálohovaný obsah obnoven do kořenové složky, přiřazujte hodnoty cílové složky jako prázdný řetězec.
* **ResolveConflict**: Instrukce, pokud dojde ke konfliktu s obnovenými daty. Přijímá **přepsání** nebo **přeskočení**.

Spusťte rutinu s parametry takto:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Příkaz vrátí úlohu s ID, které má být sledováno, jak je znázorněno v následujícím příkladu.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Obnovení souboru Azure do alternativního umístění

Pomocí [položky Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) obnovte vybraný bod obnovení. Zadejte tyto parametry k identifikaci alternativního umístění a k jednoznačné identifikaci souboru, který chcete obnovit.

* **TargetStorageAccountName**: Účet úložiště, do kterého se obnoví zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **TargetFileShareName**: Sdílené složky v rámci účtu cílového úložiště, do kterého je obnoven zálohovaný obsah.
* **TargetFolder**: Složka pod sdílenou složkou souboru, do které jsou obnovena data. Pokud má být zálohovaný obsah obnoven do kořenové složky, přiřazujte hodnoty cílové složky jako prázdný řetězec.
* **SourceFilePath**: Absolutní cesta k souboru, který má být obnoven ve sdílené složce jako řetězec. Tato cesta je stejná cesta použitá v rutině **Get-AzStorageFile** PowerShell.
* **SourceFileType**: Určuje, zda je vybrán adresář nebo soubor. Přijímá **adresář** nebo **soubor**.
* **ResolveConflict**: Instrukce, pokud dojde ke konfliktu s obnovenými daty. Přijímá **přepsání** nebo **přeskočení**.

Další parametry (SourceFilePath a SourceFileType) se vztahují pouze k jednotlivému souboru, který chcete obnovit.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Tento příkaz vrátí úlohu s ID, které má být sledováno, jak je znázorněno v předchozí části.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Obnovení sdílených složek a souborů Azure do původního umístění

Při obnovení do původního umístění není nutné zadávat parametry související s cílem a cílem. Musí být poskytnutpouze **ResolveConflict.**

#### <a name="overwrite-an-azure-file-share"></a>Přepsání sdílené složky Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Přepsání souboru Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Další kroky

[Přečtěte si o](restore-afs.md) obnovení souborů Azure na webu Azure Portal.
