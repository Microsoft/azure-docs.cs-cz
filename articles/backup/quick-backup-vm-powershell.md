---
title: Rychlý start Azure – Zálohování virtuálního počítače pomocí PowerShellu | Dokumentace Microsoftu
description: Zjistěte, jak zálohovat virtuální počítače pomocí Azure PowerShellu.
services: backup
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: ''
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 2/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 91303368f96b477f0756e4d3b9d19601797f0ba8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Zálohování virtuálního počítače v Azure pomocí PowerShellu
Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Svá data můžete chránit prováděním záloh v pravidelných intervalech. Azure Backup vytváří body obnovení, které je možné uchovávat v geograficky redundantních trezorech obnovení. Tento článek podrobně popisuje, jak zálohovat virtuální počítač pomocí modulu Azure PowerShell. K provedení těchto kroků můžete také využít [Azure CLI](quick-backup-vm-cli.md) nebo [Azure Portal](quick-backup-vm-portal.md).

V tomto rychlém startu se povolí zálohování na existujícím virtuálním počítači Azure. Pokud potřebujete vytvořit virtuální počítač, můžete [vytvořit virtuální počítač pomocí Azure PowerShellu](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Tento rychlý start vyžaduje modul Azure PowerShell verze 4.4 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="log-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

Při prvním použití služby Azure Backup musíte ve svém předplatném zaregistrovat poskytovatele služby Azure Recovery Services pomocí rutiny [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vaults"></a>Vytvoření trezoru služby Recovery Services
Trezor služby Recovery Services je logický kontejner, který uchovává zálohovaná data pro každý chráněný prostředek, například virtuální počítače Azure. Úloha zálohování pro chráněný prostředek při spuštění vytvoří uvnitř trezoru služby Recovery Services bod obnovení. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

Vytvořte trezor služby Recovery Services pomocí rutiny [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault). Zadejte stejnou skupinu prostředků a umístění, jako má virtuální počítač, který chcete chránit. Pokud jste k vytvoření virtuálního počítače použili [ukázkový skript](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json), skupina prostředků má název *myResourceGroup*, virtuální počítač má název *myVM* a prostředky jsou v umístění *WestEurope*.

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

Ve výchozím nastavení je trezor nastavený pro geograficky redundantní úložiště. Tato úroveň redundance úložiště vaše data chrání ještě více tím, že zajišťuje replikaci zálohovaných dat do sekundární oblasti Azure, která je od primární oblasti vzdálená stovky kilometrů.

Abyste mohli tento trezor použít ve zbývajících krocích, nastavte kontext trezoru pomocí rutiny [Set-AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext).

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>Povolení zálohování pro virtuální počítač Azure
K definování, kdy se spouští úloha zálohování a jak dlouho se uchovávají body obnovení, vytváříte a používáte zásady. Výchozí zásada ochrany spouští úlohu zálohování každý den a uchovává body obnovení po dobu 30 dnů. Tyto výchozí hodnoty zásady můžete použít k rychlému zajištění ochrany vašeho virtuálního počítače. Nejprve nastavte výchozí zásadu pomocí rutiny [Get-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy):

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

Pomocí rutiny [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection) povolte pro virtuální počítač ochranu zálohováním. Zadejte zásadu, která se má použít, pak skupinu prostředků a virtuální počítač, který chcete chránit:

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>Spuštění úlohy zálohování
Pokud chcete spustit zálohování ihned a nečekat na spuštění úlohy výchozí zásadou v naplánovaném čase, použijte rutinu [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem). Tato první úloha zálohování vytvoří úplný bod obnovení. Každá úloha zálohování po tomto prvotním zálohování vytváří přírůstkové body obnovení. Přírůstkové body obnovení jsou efektivní z hlediska úložiště a času, protože přenášejí pouze změny provedené od posledního zálohování.

V následující sadě příkazů zadáte kontejner v trezoru služby Recovery Services, který uchovává vaše zálohovaná data, pomocí rutiny [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer). Každý virtuální počítač určený k zálohování se považuje za položku. Pokud chcete spustit úlohu zálohování, získejte informace o položce vašeho virtuálního počítače pomocí rutiny [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem).

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Vzhledem k tomu, že první úloha zálohování vytváří úplný bod obnovení, může tento proces trvat až 20 minut.


## <a name="monitor-the-backup-job"></a>Monitorování úlohy zálohování
Pokud chcete monitorovat stav úloh zálohování, použijte rutinu [Get-AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob):

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

Výstup bude vypadat podobně jako v následujícím příkladu, který ukazuje, že je úloha zálohování ve stavu **InProgress** (Probíhající):

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

Když se *Status* (Stav) úlohy zálohovaní změní na *Completed* (Dokončeno), váš virtuální počítač je chráněný službou Recovery Services a má uložený úplný bod obnovení.


## <a name="clean-up-deployment"></a>Vyčištění nasazení
Pokud už ochrana virtuálního počítače není potřeba, můžete ji vypnout, odebrat body obnovení a trezor služby Recovery Services a pak odstranit skupinu prostředků a související prostředky virtuálního počítače. Pokud jste použili existující virtuální počítač, můžete vynechat poslední rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup), aby se zachovala skupina prostředků a virtuální počítač.

Pokud se chystáte pokračovat ke kurzu služby Backup, který vysvětluje postup obnovení dat virtuálního počítače, přeskočte kroky v této části a přejděte na [Další kroky](#next-steps). 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili trezor služby Recovery Services, povolili ochranu virtuálního počítače a vytvořili prvotní bod obnovení. Pokud se chcete o službách Azure Backup a Recovery Services dozvědět více, pokračujte ke kurzům.

> [!div class="nextstepaction"]
> [Zálohování několika virtuálních počítačů Azure](./tutorial-backup-vm-at-scale.md)
