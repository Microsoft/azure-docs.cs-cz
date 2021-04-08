---
title: Ukázkový skript PowerShellu – zálohování virtuálního počítače Azure
description: V tomto článku se dozvíte, jak pomocí ukázkového skriptu Azure PowerShell zálohovat virtuální počítač Azure.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 73dc119e8db34aed04ce8926bfa85f557027c8e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97967306"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Zálohování šifrovaného virtuálního počítače Azure pomocí PowerShellu

Tento skript vytvoří Recovery Services trezor s geograficky redundantním úložištěm (GRS) pro šifrovaný virtuální počítač Azure. Výchozí zásady ochrany se aplikují na trezor. Zásada vygeneruje denní zálohu virtuálního počítače a uchová každou zálohu po dobu 365 dnů. Skript také aktivuje počáteční bod obnovení pro virtuální počítač a uchová tento bod obnovení po dobu 30 dnů.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Vytvoří Recovery Services trezor pro ukládání záloh. |
| [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Nastaví vlastnosti úložiště zálohování v trezoru Recovery Services. |
| [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Vytvoří zásady ochrany pomocí zásad plánování a zásad uchovávání v Recovery Services trezoru. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Nastaví oprávnění ke službě Key Vault tak, že instančnímu objektu udělí přístup k šifrovacím klíčům. |
| [Enable – AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Povolí zálohování položky se zadanými zásadami ochrany před zálohováním. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Upraví existující zásady ochrany záloh. |
| [Backup – AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Spustí zálohu pro chráněnou Azure Backup položku, která není vázaná na plán zálohování. |
| [Čekání – AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Čeká na dokončení úlohy Azure Backup. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/new-azureps-module-az).
