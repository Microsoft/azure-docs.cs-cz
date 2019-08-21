---
title: Použití virtuálního počítače pro řešení potíží se systémem Linux pomocí Azure CLI | Microsoft Docs
description: Naučte se řešit potíže s virtuálním počítačem se systémem Linux připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure CLI.
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 49ee83e451e9d555a7fe5fca57bc58d6616334da
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641056"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Řešení potíží s virtuálním počítačem se systémem Linux připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure CLI
Pokud váš virtuální počítač se systémem Linux zaznamená chybu spuštění nebo disku, možná budete muset provést kroky pro řešení potíží na samotném virtuálním pevném disku. Běžným příkladem může být neplatná položka v `/etc/fstab` systému, která zabraňuje úspěšnému spuštění virtuálního počítače. Tento článek podrobně popisuje, jak pomocí rozhraní příkazového řádku Azure připojit virtuální pevný disk k jinému virtuálnímu počítači se systémem Linux a opravit případné chyby a pak znovu vytvořit původní virtuální počítač. 

## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Zastavte ovlivněný virtuální počítač.
1. Pořídit snímek z disku s operačním systémem virtuálního počítače.
1. Vytvořte disk ze snímku disku s operačním systémem.
1. Připojte nový disk s operačním systémem k jinému virtuálnímu počítači Linux pro účely řešení potíží.
1. Připojení k virtuálnímu počítači pro řešení potíží. Úpravou souborů nebo spuštěním libovolných nástrojů Opravte problémy na novém disku s operačním systémem.
1. Odpojte a odpojte nový disk s operačním systémem z virtuálního počítače pro řešení potíží.
1. Změňte disk s operačním systémem pro ovlivněný virtuální počítač.

K provedení těchto kroků při odstraňování potíží budete potřebovat nainstalovat nejnovější verzi [Azure CLI](/cli/azure/install-az-cli2) a přihlásit se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

> [!Important]
> Skripty v tomto článku se vztahují pouze na virtuální počítače, které používají [spravovaný disk](../linux/managed-disks-overview.md). 

V následujících příkladech nahraďte názvy parametrů vlastními hodnotami. Příklady názvů parametrů jsou `myResourceGroup` a `myVM`.

## <a name="determine-boot-issues"></a>Určení problémů se spouštěním
Zkontrolujte sériový výstup a určete, proč se váš virtuální počítač nemůže správně spustit. Běžným příkladem je neplatná položka v `/etc/fstab`, nebo se odstraní nebo přesune základní virtuální pevný disk.

Protokoly spouštění získáte pomocí [protokolu AZ VM Boot-Diagnostics Get-Boot-log](/cli/azure/vm/boot-diagnostics). Následující příklad získá sériový výstup z virtuálního počítače s názvem `myVM` ve skupině prostředků s názvem: `myResourceGroup`

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Přečtěte si sériový výstup a určete, proč se virtuální počítač nedaří spustit. Pokud sériový výstup neposkytuje žádné informace, možná budete muset zkontrolovat soubory protokolu v `/var/log` nástroji, když máte virtuální pevný disk připojený k virtuálnímu počítači pro řešení potíží.

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Následující příklad zastaví virtuální počítač pojmenovaný `myVM` ze skupiny prostředků s názvem: `myResourceGroup`

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Počkejte, než se virtuální počítač dokončí a teprve potom proveďte zpracování na další krok.

## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Vytvoření snímku z disku s operačním systémem virtuálního počítače

Snímek je plná kopie VHD, která je jen pro čtení. Nedá se připojit k virtuálnímu počítači. V dalším kroku vytvoříme z tohoto snímku disk. Následující příklad vytvoří snímek s názvem `mySnapshot` z disku s operačním systémem virtuálního počítače s názvem ' myVM '. 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```
## <a name="create-a-disk-from-the-snapshot"></a>Vytvoření disku ze snímku

Tento skript vytvoří spravovaný disk s názvem `newOSDisk` z snímku s názvem. `mysnapshot`  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
$storageType = 'StandardLRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Teď máte kopii původního disku s operačním systémem. Tento nový disk můžete připojit k jinému virtuálnímu počítači s Windows pro účely řešení potíží.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Připojit nový virtuální pevný disk k jinému virtuálnímu počítači
V následujících několika krocích použijete pro účely odstraňování potíží jiný virtuální počítač. Disk k tomuto virtuálnímu počítači pro odstraňování potíží připojíte, abyste mohli procházet a upravovat obsah disku. Tento proces umožňuje opravit chyby v konfiguraci nebo zkontrolovat soubory protokolu aplikace nebo systému, například. Vyberte nebo vytvořte jiný virtuální počítač, který chcete použít pro účely řešení potíží.

Připojte stávající virtuální pevný disk pomocí [AZ VM unmanaged-disk Attach](/cli/azure/vm/unmanaged-disk). Když připojíte existující virtuální pevný disk, zadejte identifikátor URI na disk získaný v předchozím `az vm show` příkazu. Následující příklad připojí existující virtuální pevný disk k virtuálnímu počítači pro řešení potíží s `myVMRecovery` názvem ve skupině prostředků s `myResourceGroup`názvem:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```
## <a name="mount-the-attached-data-disk"></a>Připojit připojený datový disk

> [!NOTE]
> Následující příklady podrobně popisují kroky vyžadované na virtuálním počítači s Ubuntu. Pokud používáte jiný distribuce pro Linux, například Red Hat Enterprise Linux nebo SUSE, umístění souborů protokolu a `mount` příkazy se můžou trochu lišit. Příslušné změny v příkazech najdete v dokumentaci pro konkrétní distribuce.

1. SSH k vašemu VIRTUÁLNÍmu počítači pro řešení potíží pomocí příslušných přihlašovacích údajů. Pokud je tento disk prvním datovým diskem připojeným k vašemu VIRTUÁLNÍmu počítači pro řešení potíží, `/dev/sdc`disk je nejspíš připojený k. Použijte `dmseg` k zobrazení připojených disků:

    ```bash
    dmesg | grep SCSI
    ```

    Výstup se podobá následujícímu příkladu:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    V předchozím příkladu je disk `/dev/sda` s operačním systémem a dočasný disk poskytnutý pro každý virtuální počítač je v. `/dev/sdb` Pokud jste měli více datových disků, měly by být v `/dev/sdd`, `/dev/sde`a tak dále.

2. Vytvořte adresář pro připojení stávajícího virtuálního pevného disku. Následující příklad vytvoří adresář s názvem `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Pokud máte na svém stávajícím virtuálním pevném disku více oddílů, připojte požadovaný oddíl. Následující příklad připojí první primární oddíl na `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Osvědčeným postupem je připojení datových disků na virtuálních počítačích v Azure s použitím univerzálně jedinečného identifikátoru (UUID) virtuálního pevného disku. Pro tento scénář krátkého řešení potíží není nutné připojení virtuálního pevného disku pomocí identifikátoru UUID. V případě normálního použití ale úpravy `/etc/fstab` pro připojení virtuálních pevných disků pomocí názvu zařízení místo identifikátoru UUID můžou způsobit selhání spuštění virtuálního počítače.


## <a name="fix-issues-on-the-new-os-disk"></a>Oprava problémů na novém disku s operačním systémem
S připojeným virtuálním pevným diskem teď můžete podle potřeby provádět libovolné kroky údržby a řešení potíží. Jakmile vyřešíte problémy, pokračujte následujícími kroky.


## <a name="unmount-and-detach-the-new-os-disk"></a>Odpojte a odpojte nový disk s operačním systémem.
Po vyřešení chyb odpojte a odpojte stávající virtuální pevný disk od virtuálního počítače pro řešení potíží. Virtuální pevný disk nemůžete použít s žádným jiným virtuálním počítačem, dokud se neuvolní zapůjčení virtuálního pevného disku k virtuálnímu počítači pro řešení potíží.

1. Z relace SSH k vašemu VIRTUÁLNÍmu počítači pro řešení potíží odpojte stávající virtuální pevný disk. Nejdřív změňte nadřazený adresář pro přípojný bod:

    ```bash
    cd /
    ```

    Nyní odpojte stávající virtuální pevný disk. Následující příklad odpojí zařízení v `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Teď odpojte virtuální pevný disk od virtuálního počítače. Ukončete relaci SSH k vašemu VIRTUÁLNÍmu počítači pro řešení potíží. Seznam připojených datových disků k VIRTUÁLNÍmu počítači pro řešení potíží pomocí příkaz [AZ VM unmanaged-disk list](/cli/azure/vm/unmanaged-disk). Následující příklad vypíše datové disky připojené k virtuálnímu počítači s `myVMRecovery` názvem ve skupině prostředků s `myResourceGroup`názvem:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Poznamenejte si název existujícího virtuálního pevného disku. Například název disku s identifikátorem URI **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** je **myVHD**. 

    Odpojte datový disk od virtuálního počítače [AZ VM unmanaged-disk detach](/cli/azure/vm/unmanaged-disk). Následující příklad odpojí disk s názvem `myVHD` z virtuálního počítače s názvem `myVMRecovery` ve `myResourceGroup` skupině prostředků:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Změna disku operačního systému pro ovlivněný virtuální počítač

K prohození disků s operačním systémem můžete použít Azure PowerShell. Nemusíte odstranit a znovu vytvořit virtuální počítač.

V tomto příkladu se zastaví virtuální `myVM` počítač s názvem a přiřadí se disk s názvem jako nový disk s `newOSDisk` operačním systémem. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Další postup
Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači, přečtěte si téma [řešení potíží s připojením SSH k virtuálnímu počítači Azure](troubleshoot-ssh-connection.md). Problémy s přístupem k aplikacím běžícím na vašem VIRTUÁLNÍm počítači najdete v tématu [řešení potíží s připojením aplikací na virtuálním počítači se systémem Linux](troubleshoot-app-connection.md).

