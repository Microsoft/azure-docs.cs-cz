---
title: Rychlý start Azure – Zálohování virtuálního počítače pomocí PowerShellu
description: Zjistěte, jak zálohovat virtuální počítače pomocí Azure PowerShellu.
author: dcurwin
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: ea4f982409f339487cd570230ebbb75682f409ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874600"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Zálohování virtuálního počítače v Azure pomocí PowerShellu

[Azure POWERSHELL AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) Module slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. 

[Azure Backup](backup-overview.md) zálohuje místní počítače a aplikace a virtuální počítače Azure. V tomto článku se dozvíte, jak zálohovat virtuální počítač Azure pomocí modulu AZ Module. Případně můžete zálohovat virtuální počítač pomocí rozhraní příkazového [řádku Azure](quick-backup-vm-cli.md)nebo [Azure Portal](quick-backup-vm-portal.md).

V tomto rychlém startu se povolí zálohování na existujícím virtuálním počítači Azure. Pokud potřebujete vytvořit virtuální počítač, můžete [vytvořit virtuální počítač pomocí Azure PowerShellu](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Tento rychlý Start vyžaduje Azure PowerShell AZ Module verze 1.0.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Přihlášení a registrace

1. Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

    ```powershell
    Connect-AzAccount
    ```
2. Při prvním použití Azure Backup musíte ve svém předplatném zaregistrovat poskytovatele služby Azure Recovery Services pomocí příkazu [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider), a to takto:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

[Recovery Services trezor](backup-azure-recovery-services-vault-overview.md) je logický kontejner, který ukládá zálohovaná data pro chráněné prostředky, jako jsou například virtuální počítače Azure. Při spuštění úlohy zálohování se v rámci Recovery Services trezoru vytvoří bod obnovení. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

Při vytváření trezoru postupujte takto:

- Pro skupinu prostředků a umístění zadejte skupinu prostředků a umístění virtuálního počítače, který chcete zálohovat.
- Pokud jste použili tento [ukázkový skript](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) k vytvoření virtuálního počítače, skupina prostředků je **myResourceGroup**, virtuální počítač je ***myVM**a prostředky jsou v oblasti **WestEurope** .
- Azure Backup automaticky zpracovává úložiště pro zálohovaná data. Ve výchozím nastavení používá trezor [geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Geografická redundance zajišťuje, že zálohovaná data se replikují do sekundární oblasti Azure a stovky kilometrů od primární oblasti.

Teď vytvořte Trezor:


1. K vytvoření trezoru použijte [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) :

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Nastavte kontext trezoru pomocí [set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)následujícím způsobem:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Změňte konfiguraci redundance úložiště (LRS/GRS) trezoru pomocí [set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty), a to následujícím způsobem:
    
    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```
    > [!NOTE]
    > Redundanci úložiště lze upravit pouze v případě, že nejsou k tomuto trezoru chráněny žádné zálohované položky.

## <a name="enable-backup-for-an-azure-vm"></a>Povolení zálohování pro virtuální počítač Azure

Povolíte zálohování pro virtuální počítač Azure a zadáte zásady zálohování.

- Zásady definují, kdy se zálohování spustí, a jak dlouho se mají zachovat body obnovení vytvořené zálohami.
- Výchozí zásada ochrany spouští zálohování jednou denně pro virtuální počítač a zachovává vytvořené body obnovení po dobu 30 dnů. Pomocí této výchozí zásady můžete rychle chránit svůj virtuální počítač. 

Zapněte zálohování následujícím způsobem:

1. Nejdřív nastavte výchozí zásadu pomocí [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Povolte zálohování virtuálních počítačů pomocí [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Zadejte zásadu, skupinu prostředků a název virtuálního počítače.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Spuštění úlohy zálohování

Zálohy běží v souladu s plánem zadaným v zásadách zálohování. Můžete také spustit zálohování ad hoc:

- První úloha počátečního zálohování vytvoří úplný bod obnovení.
- Po počátečním zálohování Každá úloha zálohování vytvoří přírůstkové body obnovení.
- Přírůstkové body obnovení jsou efektivní z hlediska úložiště a času, protože přenášejí pouze změny provedené od posledního zálohování.

Pokud chcete spustit zálohování ad hoc, použijte [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem). 
- V trezoru zadáte kontejner, který uchovává data záloh pomocí [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Každý virtuální počítač určený k zálohování se považuje za položku. Pokud chcete spustit úlohu zálohování, získáte informace o virtuálním počítači pomocí [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Spusťte úlohu zálohování ad hoc následujícím způsobem:

1. Zadejte kontejner, Získejte informace o virtuálním počítači a spusťte zálohování.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Možná budete muset počkat až 20 minut, protože první úloha zálohování vytvoří úplný bod obnovení. Sledujte úlohu, jak je popsáno v následujícím postupu.


## <a name="monitor-the-backup-job"></a>Monitorování úlohy zálohování

1. Spuštěním rutiny [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) můžete monitorovat stav úlohy.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```
    Výstup je podobný následujícímu příkladu, který ukazuje úlohu jako neprůběh:

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Po **dokončení**stavu úlohy je virtuální počítač chráněný a je uložený úplný bod obnovení.


## <a name="clean-up-the-deployment"></a>Vyčištění nasazení

Pokud už nepotřebujete zálohovat virtuální počítač, můžete ho vyčistit.
- Pokud chcete vyzkoušet obnovení virtuálního počítače, přeskočte vyčištění.
- Pokud jste použili existující virtuální počítač, můžete přeskočit poslední rutinu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) a ponechat skupinu prostředků a virtuální počítač na místě.

Zakažte ochranu, odeberte body obnovení a trezor. Pak odstraňte skupinu prostředků a související prostředky virtuálního počítače následujícím způsobem:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili trezor služby Recovery Services, povolili ochranu virtuálního počítače a vytvořili prvotní bod obnovení. 

- Naučte se, [jak](tutorial-backup-vm-at-scale.md) zálohovat virtuální počítače v Azure Portal.
- [Zjistěte, jak](tutorial-restore-disk.md) rychle obnovit virtuální počítač.
