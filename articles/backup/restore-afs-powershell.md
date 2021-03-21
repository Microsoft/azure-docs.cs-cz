---
title: Obnovení souborů Azure pomocí PowerShellu
description: V tomto článku se dozvíte, jak obnovit soubory Azure pomocí služby Azure Backup a PowerShellu.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 60c9848e12de80bcafe4553a9e8f3e27e8876d41
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021380"
---
# <a name="restore-azure-files-with-powershell"></a>Obnovení souborů Azure pomocí PowerShellu

Tento článek vysvětluje, jak obnovit celou sdílenou složku nebo konkrétní soubory z bodu obnovení vytvořeného službou [Azure Backup](backup-overview.md) pomocí Azure PowerShell.

Můžete obnovit celou sdílenou složku nebo konkrétní soubory ve sdílené složce. Můžete obnovit do původního umístění nebo do alternativního umístění.

> [!WARNING]
> Ujistěte se, že je verze prostředí PowerShell upgradována na minimální verzi příkazu AZ. RecoveryServices 2.6.0 pro zálohy na AFS. Další informace najdete v [části](backup-azure-afs-automation.md#important-notice-backup-item-identification) popisující požadavek na tuto změnu.

>[!NOTE]
>Azure Backup teď podporuje obnovení více souborů nebo složek do původního nebo alternativního umístění pomocí PowerShellu. Další informace najdete v [této části](#restore-multiple-files-or-folders-to-original-or-alternate-location) dokumentu.

## <a name="fetch-recovery-points"></a>Načíst body obnovení

K vypsání všech bodů obnovení pro zálohovanou položku použijte [příkaz Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) .

V následujícím skriptu:

* Proměnná **$RP** je pole bodů obnovení pro vybranou zálohovanou položku z posledních sedmi dnů.
* Pole je seřazené v opačném pořadí s nejnovějším bodem obnovení na indexu **0**.
* Pro výběr bodu obnovení použijte standardní indexování pole v PowerShellu.
* V příkladu **$RP [0]** vybere nejnovější bod obnovení.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"
$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID
$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
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

Po výběru příslušného bodu obnovení obnovte sdílenou složku nebo soubor do původního umístění nebo do alternativního umístění.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Obnovení sdílené složky Azure do alternativního umístění

K obnovení do vybraného bodu obnovení použijte [příkaz Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) . Zadejte tyto parametry pro identifikaci alternativního umístění:

* **TargetStorageAccountName**: účet úložiště, do kterého se obnovil zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **TargetFileShareName**: sdílené složky v rámci cílového účtu úložiště, do kterého se obnoví zálohovaný obsah.
* **TargetFolder**: složka ve sdílené složce, do které se budou data obnovovat. Pokud bude zálohovaný obsah obnoven do kořenové složky, zadejte hodnoty cílové složky jako prázdný řetězec.
* **ResolveConflict selhalo**: instrukce, pokud dojde ke konfliktu s obnovenými daty. Přijímá **přepis** nebo **Skip**.

Spusťte rutinu s parametry následujícím způsobem:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Příkaz vrátí úlohu s ID, které se má sledovat, jak je znázorněno v následujícím příkladu.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Obnovení souboru Azure do alternativního umístění

K obnovení do vybraného bodu obnovení použijte [příkaz Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) . Určete tyto parametry k identifikaci alternativního umístění a k jedinečné identifikaci souboru, který chcete obnovit.

* **TargetStorageAccountName**: účet úložiště, do kterého se obnovil zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **TargetFileShareName**: sdílené složky v rámci cílového účtu úložiště, do kterého se obnoví zálohovaný obsah.
* **TargetFolder**: složka ve sdílené složce, do které se budou data obnovovat. Pokud bude zálohovaný obsah obnoven do kořenové složky, zadejte hodnoty cílové složky jako prázdný řetězec.
* **SourceFilePath**: absolutní cesta k souboru, která se má obnovit v rámci sdílené složky jako řetězec. Tato cesta je stejná jako cesta použitá v rutině PowerShellu **Get-AzStorageFile** .
* **SourceFileType**: Určuje, zda je vybrán adresář nebo soubor. Přijímá **adresář** nebo **soubor**.
* **ResolveConflict selhalo**: instrukce, pokud dojde ke konfliktu s obnovenými daty. Přijímá **přepis** nebo **Skip**.

Další parametry (SourceFilePath a SourceFileType) se týkají pouze jednotlivých souborů, které chcete obnovit.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Tento příkaz vrátí úlohu s ID, které se má sledovat, jak je znázorněno v předchozí části.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Obnovení sdílených složek a souborů Azure do původního umístění

Při obnovení do původního umístění nemusíte zadávat parametry související s cíli a cíli. Musí být zadáno pouze **ResolveConflict selhalo** .

### <a name="overwrite-an-azure-file-share"></a>Přepsání sdílené složky Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>Přepsat soubor Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Obnovení více souborů nebo složek do původního nebo alternativního umístění

Použijte příkaz [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) předáním cesty všech souborů nebo složek, které chcete obnovit, jako hodnotu parametru **MultipleSourceFilePath** .

### <a name="restore-multiple-files"></a>Obnovení více souborů

V následujícím skriptu se snažíme obnovit soubory *FileSharePage.png* a *MyTestFile.txt* .

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>Obnovení více adresářů

V následujícím skriptu se snažíme obnovit adresáře *zrs1_restore* a *obnovení* .

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

Výstup se bude podobat tomuto:

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

Pokud chcete obnovit více souborů nebo složek do alternativního umístění, použijte výše uvedené skripty zadáním hodnot cílových parametrů souvisejících s umístěním, jak je popsáno výše v části [obnovení souboru Azure do alternativního umístění](#restore-an-azure-file-to-an-alternate-location).

## <a name="next-steps"></a>Další kroky

[Přečtěte si o](restore-afs.md) obnovení souborů Azure v Azure Portal.
