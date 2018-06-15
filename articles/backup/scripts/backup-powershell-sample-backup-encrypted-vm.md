---
title: Azure ukázkový skript prostředí PowerShell – zálohuje virtuální počítač Azure | Microsoft Docs
description: Azure ukázkový skript prostředí PowerShell – zálohuje virtuální počítač Azure
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: backup
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/07/2017
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: 4376add4a2e51806bd5db228ad2fe2afcf2e4f57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842647"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Zálohování šifrované virtuálního počítače Azure pomocí prostředí PowerShell

Tento skript vytvoří trezoru služeb zotavení s geograficky redundantní úložiště (GRS) pro šifrované virtuální počítač Azure. Do trezoru se uplatní výchozí zásady ochrany. Zásady generuje denní zálohování pro virtuální počítač a každá záloha uchovává po dobu 30 dnů. Skript také vytvoření počátečního bodu obnovení pro virtuální počítač se aktivuje a bude mít tento bod obnovení pro 365 dnů. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spusťte následující příkaz pro odebrání skupiny prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k vytvoření nasazení. Každou položku v tabulce odkazy na dokumentaci konkrétní příkaz.


| Příkaz | Poznámky | 
|---|---| 
| [Nový AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve kterém jsou uložené všechny prostředky. | 
| [Nové AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Vytvoří trezoru služeb zotavení pro ukládání záloh. | 
| [Set-AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | Nastaví zálohování vlastností úložiště na trezor služeb zotavení. | 
| [Nové AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Vytvoří zásady ochrany pomocí plán zásad a zásad uchovávání informací v trezoru služeb zotavení. | 
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Nastaví oprávnění pro Key Vault udělit přístup k hlavní službě pro šifrovací klíče. | 
| [Povolit AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Povolí zálohování pro položku se zadanou zásady ochrany zálohování. | 
| [Set-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Upravuje existující zásady zálohování ochrany. | 
| [Zálohování AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Spustí zálohování pro chráněné položky zálohování Azure, který není vázaný na plán zálohování. |
| [Počkejte AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Čeká na dokončení úlohy Azure Backup. | 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odebere skupinu prostředků a všechny prostředky obsažené v rámci. | 

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShell najdete v tématu [dokumentace Azure PowerShell](/powershell/azure/overview).

