---
title: Rychlý start Azure – Zálohování virtuálního počítače pomocí PowerShellu
description: Zjistěte, jak zálohovat virtuální počítače pomocí Azure PowerShellu.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: aa637571ca11ea294b1f95df49855d7ee81b3001
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258866"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Zálohování virtuálního počítače v Azure pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Prostředí Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) modul se používá k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. 

[Azure Backup](backup-overview.md) zálohuje místních počítačů a aplikací a virtuálních počítačů Azure. V tomto článku se dozvíte, jak zálohovat virtuální počítač Azure s modulem AZ. Alternativně můžete zálohovat virtuální počítač pomocí [rozhraní příkazového řádku Azure](quick-backup-vm-cli.md), nebo [webu Azure portal](quick-backup-vm-portal.md).

V tomto rychlém startu se povolí zálohování na existujícím virtuálním počítači Azure. Pokud potřebujete vytvořit virtuální počítač, můžete [vytvořit virtuální počítač pomocí Azure PowerShellu](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Tento rychlý start vyžaduje prostředí Azure PowerShell AZ modulu verze 1.0.0 nebo novějším. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="log-in-and-register"></a>Přihlaste se a registrace

1. Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

    ```powershell
    Connect-AzAccount
    ```
2. Při prvním použití služby Azure Backup, zaregistrujte poskytovatele služeb zotavení Azure ve vašem předplatném pomocí [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider), následujícím způsobem:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

A [trezor služby Recovery Services](backup-azure-recovery-services-vault-overview.md) je logický kontejner, který zálohuje data na chráněný prostředek, jako jsou virtuální počítače Azure. Když se spouští úloha zálohování, vytvoří bod obnovení v trezoru služby Recovery Services. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

Při vytváření trezoru:

- Pro skupinu prostředků a umístění zadejte skupinu prostředků a umístění virtuálního počítače, které chcete zálohovat.
- Pokud jste použili toto [ukázkový skript](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) vytvořte virtuální počítač, skupina prostředků je **myResourceGroup**, je virtuální počítač ***myVM**, a jsou prostředky v **WestEurope**  oblasti.
- Azure Backup automaticky zpracovává úložiště pro zálohovaná data. Ve výchozím nastavení používá trezor [geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Geografická redundance zajišťuje, že zálohovat data se replikují do sekundární oblasti Azure, stovky mil od primární oblasti.

Teď vytvořte trezor.


1. Použití [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)vytvoření trezoru:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Nastavte kontext trezoru pomocí [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext), následujícím způsobem:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```


## <a name="enable-backup-for-an-azure-vm"></a>Povolení zálohování pro virtuální počítač Azure

Povolení zálohování pro virtuální počítač Azure a zadejte zásady zálohování.

- Zásada se definuje při spuštění zálohování, a jak dlouho se uchovávají body obnovení vytvořené zálohy.
- Výchozí zásada ochrany spouští jednou denně zálohování pro virtuální počítač a uchovává body obnovení vytvořené po dobu 30 dnů. Tento výchozí zásady můžete použít k rychlému zajištění ochrany vašeho virtuálního počítače. 

Povolte zálohování následujícím způsobem:

1. Nejprve nastavte výchozí zásadu pomocí [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Povolit zálohování virtuálních počítačů s [povolit AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Zadejte zásady, skupiny prostředků a název virtuálního počítače.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```


## <a name="start-a-backup-job"></a>Spuštění úlohy zálohování

Zálohování se spouštějí podle plánu, zadaný v zásadě zálohování. Můžete také spouštět ad hoc zálohování:

- První úlohy prvotního zálohování vytvoří úplný bod obnovení.
- Po prvotní zálohy Každá úloha zálohování vytváří přírůstkové body obnovení.
- Přírůstkové body obnovení jsou efektivní z hlediska úložiště a času, protože přenášejí pouze změny provedené od posledního zálohování.

Chcete-li spustit zálohování ad hoc, použijte[zálohování AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem). 
- Zadejte kontejner v trezoru, který uchovává vaše zálohovaná data s [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Každý virtuální počítač určený k zálohování se považuje za položku. Spustit úlohu zálohování, získejte informace o virtuálním počítači s [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Spuštění úlohy zálohování ad hoc následujícím způsobem:

1. Zadejte kontejner, získat informace o virtuálním počítači a spusťte zálohování.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Můžete potřebovat čekat až 20 minut, protože první úloha zálohování vytváří úplný bod obnovení. Monitorování úlohy, jak je popsáno v následujícím postupu.


## <a name="monitor-the-backup-job"></a>Monitorování úlohy zálohování

1. Spustit [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) monitorovat stav úlohy.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```
    Výstup je podobný následujícím příkladu, který ukazuje úlohy jako **InProgress**:

    ```
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Pokud je stav úlohy **dokončeno**, je chráněný virtuální počítač a uložil na úplný bod obnovení.


## <a name="clean-up-the-deployment"></a>Vyčištění nasazení

Pokud už nepotřebujete zálohování virtuálního počítače, můžete vyčistit ho.
- Pokud chcete vyzkoušet obnovení virtuálního počítače, přejděte vyčištění nahoru.
- Pokud jste použili existující virtuální počítač, můžete vynechat poslední [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) rutiny tak, aby se zachovala skupina prostředků a virtuální počítač.

Zakažte ochranu, odebrat body obnovení a trezor. Pak můžete odstranit skupinu prostředků a související prostředky virtuálního počítače následujícím způsobem:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili trezor služby Recovery Services, povolili ochranu virtuálního počítače a vytvořili prvotní bod obnovení. 

- [Zjistěte, jak](tutorial-backup-vm-at-scale.md) k zálohování virtuálních počítačů na webu Azure Portal.
- [Zjistěte, jak](tutorial-restore-disk.md) rychle obnovit virtuální počítač
