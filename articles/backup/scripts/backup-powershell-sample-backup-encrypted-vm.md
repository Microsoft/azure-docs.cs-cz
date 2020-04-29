---
title: Ukázkový skript PowerShellu – zálohování virtuálního počítače Azure
description: V tomto článku se dozvíte, jak pomocí ukázkového skriptu Azure PowerShell zálohovat virtuální počítač Azure.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: ad4d290961bd39659283795f6a58ccb815b639b0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "74171805"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Zálohování šifrovaného virtuálního počítače Azure pomocí PowerShellu

Tento skript vytvoří Recovery Services trezor s geograficky redundantním úložištěm (GRS) pro šifrovaný virtuální počítač Azure. Výchozí zásady ochrany se aplikují na trezor. Zásada vygeneruje denní zálohu virtuálního počítače a uchová každou zálohu po dobu 30 dnů. Skript také aktivuje počáteční bod obnovení pro virtuální počítač a uchová tento bod obnovení po dobu 365 dnů.

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
| [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Vytvoří trezor služby Recovery Services pro ukládání záloh. |
| [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Nastaví vlastnosti úložiště zálohování v trezoru Recovery Services. |
| [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Vytvoří zásady ochrany pomocí zásad plánování a zásad uchovávání v Recovery Services trezoru. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Nastaví oprávnění ke službě Key Vault tak, že instančnímu objektu udělí přístup k šifrovacím klíčům. |
| [Enable – AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Povolí zálohování položky se zadanými zásadami ochrany před zálohováním. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Upraví existující zásady ochrany záloh. |
| [Backup – AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Spustí zálohu pro chráněnou Azure Backup položku, která není vázaná na plán zálohování. |
| [Čekání – AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Čeká na dokončení úlohy Azure Backup. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).
