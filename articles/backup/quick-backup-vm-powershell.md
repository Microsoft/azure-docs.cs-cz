---
title: Úvodní příručka – zálohování virtuálního virtuálního aplikace pomocí Prostředí PowerShell
description: V tomto rychlém startu se dozvíte, jak zálohovat virtuální počítače Azure pomocí modulu Azure PowerShell.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc
ms.openlocfilehash: 8021ca553a1434c891bee911e85d351c61938594
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74171948"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Zálohování virtuálního počítače v Azure pomocí PowerShellu

Modul [Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) se používá k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech.

[Azure Backup](backup-overview.md) zálohuje místní počítače a aplikace a virtuální počítače Azure. Tento článek ukazuje, jak zálohovat virtuální počítač Azure pomocí modulu AZ. Případně můžete zálohovat virtuální počítač pomocí [Azure CLI](quick-backup-vm-cli.md)nebo na [webu Azure Portal](quick-backup-vm-portal.md).

V tomto rychlém startu se povolí zálohování na existujícím virtuálním počítači Azure. Pokud potřebujete vytvořit virtuální počítač, můžete [vytvořit virtuální počítač pomocí Azure PowerShellu](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Tento rychlý start vyžaduje modul Azure PowerShell AZ verze 1.0.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Přihlaste se a zaregistrujte se

1. Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

    ```powershell
    Connect-AzAccount
    ```

2. Při prvním použití služby Azure Backup je nutné zaregistrovat poskytovatele služby Azure Recovery Service ve svém předplatném pomocí [register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider)takto:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

[Trezor služby Recovery Services](backup-azure-recovery-services-vault-overview.md) je logický kontejner, který ukládá záložní data pro chráněné prostředky, jako jsou virtuální počítače Azure. Při spuštění úlohy zálohování vytvoří bod obnovení uvnitř trezoru služby Recovery Services. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

Při vytváření trezoru:

- Pro skupinu prostředků a umístění zadejte skupinu prostředků a umístění virtuálního virtuálního ms, který chcete zálohovat.
- Pokud jste tento [ukázkový skript](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) použili k vytvoření virtuálního virtuálního pracovního jazyka, skupina prostředků je **myResourceGroup**, virtuální hod je ***myVM**a prostředky jsou v oblasti **WestEurope.**
- Azure Backup automaticky zpracovává úložiště pro zálohovaná data. Ve výchozím nastavení používá úložiště [geograficky redundantní úložiště (GRS).](../storage/common/storage-redundancy-grs.md) Geografická redundance zajišťuje, že zálohovaná data se replikují do sekundární oblasti Azure, stovky kilometrů od primární oblasti.

Nyní vytvořte úschovnu:

1. K vytvoření trezoru použijte trezor [New-AzRecoveryServicesVault:](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Nastavte kontext úschovny pomocí [sady Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)takto:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Změňte konfiguraci redundance úložiště (LRS/GRS) trezoru pomocí [vlastnosti Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty)takto:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > Redundanci úložiště lze upravit pouze v případě, že neexistují žádné položky zálohování chráněné do trezoru.

## <a name="enable-backup-for-an-azure-vm"></a>Povolení zálohování pro virtuální počítač Azure

Povolíte zálohování pro virtuální počítač Azure a určíte zásady zálohování.

- Zásada definuje, kdy jsou zálohy spuštěny, a jak dlouho by měly být zachovány body obnovení vytvořené zálohami.
- Výchozí zásady ochrany spustí zálohu jednou denně pro virtuální počítače a zachová vytvořené body obnovení po dobu 30 dnů. Pomocí této výchozí zásady můžete rychle chránit virtuální počítač.

Povolit zálohování takto:

1. Nejprve nastavte výchozí zásadu pomocí [zásad Get-AzRecoveryServicesBackupProtectionProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Povolte zálohování virtuálního počítače pomocí [ochrany Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Zadejte zásadu, skupinu prostředků a název virtuálního soudu.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Spuštění úlohy zálohování

Zálohy jsou spuštěny podle plánu určeného v zásadách zálohování. Můžete také spustit zálohu na vyžádání:

- První počáteční úloha zálohování vytvoří úplný bod obnovení.
- Po počáteční mši vytvoří každá úloha zálohování přírůstkové body obnovení.
- Přírůstkové body obnovení jsou efektivní z hlediska úložiště a času, protože přenášejí pouze změny provedené od posledního zálohování.

Chcete-li spustit zálohu na vyžádání, použijte [položku Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- V úschovně zadáte kontejner, který obsahuje záložní data pomocí [kontejneru Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Každý virtuální počítač určený k zálohování se považuje za položku. Chcete-li spustit úlohu zálohování, získáte informace o virtuálním počítače s [službou Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Spusťte úlohu zálohování na vyžádání následujícím způsobem:

1. Zadejte kontejner, získejte informace o virtuálním počítače a spusťte zálohu.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Možná budete muset počkat až 20 minut, protože první úloha zálohování vytvoří úplný bod obnovení. Sledujte úlohu, jak je popsáno v dalším postupu.

## <a name="monitor-the-backup-job"></a>Monitorování úlohy zálohování

1. Spusťte [get-azRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) pro sledování stavu úlohy.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    Výstup je podobný následujícímu příkladu, který zobrazuje úlohu jako **InProgress**:

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Když je stav **úlohy dokončen**, virtuální počítač je chráněný a má uložený úplný bod obnovení.

## <a name="clean-up-the-deployment"></a>Vyčištění nasazení

Pokud už nepotřebujete zálohovat virtuální ho, můžete ho vyčistit.

- Pokud chcete vyzkoušet obnovení virtuálního virtuálního mísy, přeskočte vyčištění.
- Pokud jste použili existující virtuální hod, můžete přeskočit konečné [Odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) rutina opustit skupinu prostředků a virtuálního virtuálního trhu na místě.

Zakažte ochranu, odstraňte body obnovení a trezor. Potom odstraňte skupinu prostředků a přidružené prostředky virtuálního soudu takto:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor služby Recovery Services, povolili ochranu virtuálního počítače a vytvořili prvotní bod obnovení.

- [Přečtěte si, jak](tutorial-backup-vm-at-scale.md) zálohovat virtuální počítače na webu Azure Portal.
- [Přečtěte si, jak](tutorial-restore-disk.md) rychle obnovit virtuální hod.
