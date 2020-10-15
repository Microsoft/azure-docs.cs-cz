---
title: Použití virtuálního počítače pro řešení potíží se systémem Linux pomocí Azure CLI | Microsoft Docs
description: Naučte se řešit potíže s virtuálním počítačem se systémem Linux připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure CLI.
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: bc796b4d9708c18f4d0612e6f3ebde9b2485d2f7
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093136"
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

Pomocí příkazů pro opravu virtuálního počítače můžete automatizovat kroky 1, 2, 3, 4, 6 a 7. Další dokumentaci a pokyny najdete v tématu [Oprava virtuálního počítače se systémem Linux pomocí příkazů pro opravu virtuálního počítače Azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!Important]
> Skripty v tomto článku se vztahují pouze na virtuální počítače, které používají [spravovaný disk](../managed-disks-overview.md). 

V následujících příkladech nahraďte názvy parametrů vlastními hodnotami, například `myResourceGroup` a `myVM` .

## <a name="determine-boot-issues"></a>Určení problémů se spouštěním
Zkontrolujte sériový výstup a určete, proč se váš virtuální počítač nemůže správně spustit. Běžným příkladem je neplatná položka v `/etc/fstab` , nebo se odstraní nebo přesune základní virtuální pevný disk.

Protokoly spouštění získáte pomocí [protokolu AZ VM Boot-Diagnostics Get-Boot-log](/cli/azure/vm/boot-diagnostics). Následující příklad získá sériový výstup z virtuálního počítače s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup` :

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Přečtěte si sériový výstup a určete, proč se virtuální počítač nedaří spustit. Pokud sériový výstup neposkytuje žádné informace, možná budete muset zkontrolovat soubory protokolu v nástroji, `/var/log` když máte virtuální pevný disk připojený k virtuálnímu počítači pro řešení potíží.

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Následující příklad zastaví virtuální počítač pojmenovaný `myVM` ze skupiny prostředků s názvem `myResourceGroup` :

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Pořízení snímku z disku s operačním systémem ovlivněného virtuálního počítače

Snímek je plná kopie VHD, která je jen pro čtení. Nedá se připojit k virtuálnímu počítači. V dalším kroku vytvoříme z tohoto snímku disk. Následující příklad vytvoří snímek s názvem `mySnapshot` z disku s operačním systémem virtuálního počítače s názvem ' myVM '. 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Vytvoření disku ze snímku

Tento skript vytvoří spravovaný disk s názvem `myOSDisk` z snímku s názvem `mySnapshot` .  

```azurecli
#Provide the name of your resource group
$resourceGroup="myResourceGroup"

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot="mySnapshot"

#Provide the name of the Managed Disk
$osDisk="myNewOSDisk"

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType="Premium_LRS"

#Provide the OS type
$osType="linux"

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query id -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Pokud skupina prostředků a zdrojový snímek nejsou ve stejné oblasti, zobrazí se při spuštění chyba "prostředek nebyl nalezen" `az disk create` . V takovém případě musíte určit, že se má `--location <region>` disk vytvořit ve stejné oblasti jako zdrojový snímek.

Teď máte kopii původního disku s operačním systémem. Tento nový disk můžete připojit k jinému virtuálnímu počítači s Windows pro účely řešení potíží.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Připojit nový virtuální pevný disk k jinému virtuálnímu počítači
V následujících několika krocích použijete pro účely odstraňování potíží jiný virtuální počítač. Disk k tomuto virtuálnímu počítači pro odstraňování potíží připojíte, abyste mohli procházet a upravovat obsah disku. Tento proces umožňuje opravit chyby konfigurace nebo zkontrolovat další soubory protokolu aplikace nebo systému.

Tento skript připojí disk `myNewOSDisk` k virtuálnímu počítači `MyTroubleshootVM` :

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az disk show -g $resourceGroup -n $osDisk --query id -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $myNewOSDiskid --resource-group $resourceGroup --size-gb $diskSize --sku $storageType --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>Připojit připojený datový disk

> [!NOTE]
> Následující příklady podrobně popisují kroky vyžadované na virtuálním počítači s Ubuntu. Pokud používáte jiný distribuce pro Linux, například Red Hat Enterprise Linux nebo SUSE, umístění souborů protokolu a `mount` příkazy se můžou trochu lišit. Příslušné změny v příkazech najdete v dokumentaci pro konkrétní distribuce.

1. SSH k vašemu VIRTUÁLNÍmu počítači pro řešení potíží pomocí příslušných přihlašovacích údajů. Pokud je tento disk prvním datovým diskem připojeným k vašemu VIRTUÁLNÍmu počítači pro řešení potíží, disk je nejspíš připojený k `/dev/sdc` . Použijte `dmesg` k zobrazení připojených disků:

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

    V předchozím příkladu je disk s operačním systémem `/dev/sda` a dočasný disk poskytnutý pro každý virtuální počítač je v `/dev/sdb` . Pokud jste měli více datových disků, měly by být v `/dev/sdd` , `/dev/sde` a tak dále.

2. Vytvořte adresář pro připojení stávajícího virtuálního pevného disku. Následující příklad vytvoří adresář s názvem `troubleshootingdisk` :

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Pokud máte na svém stávajícím virtuálním pevném disku více oddílů, připojte požadovaný oddíl. Následující příklad připojí první primární oddíl na `/dev/sdc1` :

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

    Nyní odpojte stávající virtuální pevný disk. Následující příklad odpojí zařízení v `/dev/sdc1` :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Teď odpojte virtuální pevný disk od virtuálního počítače. Ukončete relaci SSH na VIRTUÁLNÍm počítači pro řešení potíží:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Změna disku operačního systému pro ovlivněný virtuální počítač

K prohození disků s operačním systémem můžete použít rozhraní příkazového řádku Azure. Nemusíte odstranit a znovu vytvořit virtuální počítač.

V tomto příkladu se zastaví virtuální počítač s názvem `myVM` a přiřadí se disk s názvem `myNewOSDisk` jako nový disk s operačním systémem.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g $resourceGroup -n $osDisk --query id -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>Další kroky
Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači, přečtěte si téma [řešení potíží s připojením SSH k virtuálnímu počítači Azure](troubleshoot-ssh-connection.md). Problémy s přístupem k aplikacím běžícím na vašem VIRTUÁLNÍm počítači najdete v tématu [řešení potíží s připojením aplikací na virtuálním počítači se systémem Linux](troubleshoot-app-connection.md).

