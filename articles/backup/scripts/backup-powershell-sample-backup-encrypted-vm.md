---
title: Ukázkový skript Azure Powershellu – zálohování virtuálního počítače Azure | Dokumentace Microsoftu
description: Ukázkový skript Azure Powershellu – zálohování virtuálního počítače Azure
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: backup
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/07/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: eb22dc88c971e0ddc293fabd64bfd30145b2edd1
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651380"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Zálohování šifrovaných virtuálních počítačů Azure pomocí Powershellu

Tento skript vytvoří trezor služby Recovery Services s geograficky redundantní úložiště (GRS) pro šifrované virtuální počítač Azure. Výchozí zásada ochrany platí do trezoru. Zásady generuje denní zálohování pro virtuální počítač a každá záloha uchovává po dobu 30 dnů. Skript také aktivuje prvotní bod obnovení pro virtuální počítač a uchovává tento bod obnovení po dobu 365 dnů. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.


| Příkaz | Poznámky | 
|---|---| 
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. | 
| [Nový AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Vytvoří trezor služby recovery services k ukládání záloh. | 
| [Set-AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | Nastaví zálohování vlastnosti úložiště pro trezor služby Recovery Services. | 
| [Nový AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Vytvoří zásady ochrany pomocí zásady plánování a zásady uchovávání informací v trezoru služby Recovery Services. | 
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Nastaví oprávnění ke službě Key Vault tak, že instančnímu objektu udělí přístup k šifrovacím klíčům. | 
| [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Povolí zálohování pro položku zadané zásady ochrany zálohování. | 
| [Set-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Upraví stávající zásady ochrany zálohování. | 
| [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Spuštění zálohování pro chráněnou položku Azure Backup, která se neváže na plán zálohování. |
| [Wait-AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Čeká na dokončení úlohy Azure Backup. | 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. | 

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

