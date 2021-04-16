---
title: Obnovení Azure Managed Disks přes Azure PowerShell
description: Naučte se, jak obnovit Managed Disks Azure pomocí Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: c6625b43c313d45d4b295dd406e29a2b1d85b387
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520033"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Obnovení Azure Managed Disks pomocí Azure PowerShell

Tento článek vysvětluje, jak obnovit [Azure Managed disks](../virtual-machines/managed-disks-overview.md) z bodu obnovení vytvořeného pomocí Azure Backup.

V současné době se možnost obnovení Original-Location (OLR) obnovuje nahrazením stávajícího zdrojového disku, ze kterého se zálohy provedly, ale nepodporují. Můžete obnovit z bodu obnovení a vytvořit nový disk ve stejné skupině prostředků jako zdrojový disk, ze kterého byly zálohy pořízeny nebo v jakékoli jiné skupině prostředků. Tento postup se označuje Alternate-Location jako ALR Recovery () a pomáhá udržet zdrojový disk i obnovený (nový) disk.

V tomto článku se naučíte:

- Obnovit pro vytvoření nového disku

- Sledovat stav operace obnovení

V příkladech se v části Skupina prostředků "testBkpVaultRG" odkazuje na existující úložiště záloh "TestBkpVault".

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Obnovit pro vytvoření nového disku

### <a name="setting-up-permissions"></a>Nastavení oprávnění

Úložiště záloh používá spravovanou identitu pro přístup k dalším prostředkům Azure. Pro obnovení ze zálohy vyžaduje spravovaná identita trezoru záloh sadu oprávnění pro skupinu prostředků, do které se má disk obnovit.

Úložiště záloh používá spravovanou identitu přiřazenou systémem, která je omezená na jeden prostředek a je vázaná na životní cyklus tohoto prostředku. Pomocí řízení přístupu na základě role Azure (RBAC) můžete udělit oprávnění ke spravované identitě. Spravovaná identita je instanční objekt speciálního typu, který se dá použít jenom s prostředky Azure. Přečtěte si další informace o [spravovaných identitách](../active-directory/managed-identities-azure-resources/overview.md).

Přiřaďte relevantní oprávnění pro spravovanou identitu přiřazenou systémem v rámci cílové skupiny prostředků, kde se tyto disky obnoví nebo vytvoří, jak je uvedeno [zde](restore-managed-disks.md#restore-to-create-a-new-disk).

### <a name="fetching-the-relevant-recovery-point"></a>Načítají se relevantní body obnovení.

Načtěte všechny instance pomocí příkazu [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) a Identifikujte relevantní instanci.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

K vyhledávání napříč instancemi v mnoha trezorech a předplatných můžete použít také příkaz **AZ. Resourcegraph** a [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

Po identifikaci instance načtěte příslušný bod obnovení.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>Příprava žádosti o obnovení

Sestavte ID ARM nového disku, který se má vytvořit, s cílovou skupinou prostředků, ke kterému byla přiřazena oprávnění [podrobná a](#setting-up-permissions)požadovaný název disku. Například disk může mít název **PSTestDisk2** pod skupinou prostředků **targetrg** s jiným předplatným.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Pomocí příkazu [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) Připravte požadavek na obnovení se všemi relevantními podrobnostmi.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Aktivace obnovení

Pomocí příkazu [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) aktivujte obnovení s výše připraveným požadavkem.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Úloha sledování

Sledujte všechny úlohy pomocí příkazu [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) . Můžete vypsat všechny úlohy a načíst konkrétní podrobnosti o úloze.

Pomocí **AZ. ResourceGraph** můžete také sledovat všechny úlohy ve všech úložištích služby Backup. Pomocí příkazu [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) získáte relevantní úlohu, která může být v jakémkoli úložišti záloh.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Další kroky

- [Nejčastější dotazy k Azure disk Backup](disk-backup-faq.yml)
