---
title: Použití virtuálního počítače pro řešení potíží s Linuxem s azure cli | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit problémy s virtuálním počítačem s Linuxem připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí příkazového příkazového příkazu Azure
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
ms.openlocfilehash: 1b91a39e1297d8952da67a4f8d3b8568cefe04ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73620567"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Poradce při potížích s virtuálním počítačem s Linuxem připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí příkazového příkazového příkazu azure
Pokud váš virtuální počítač (VM) linux (VM) narazí na chybu při spuštění nebo disku, budete muset provést kroky řešení potíží na virtuálním pevném disku sám. Běžným příkladem by byla `/etc/fstab` neplatná položka, která brání úspěšnému spuštění virtuálního virtuálního movitého virtuálního objektu. Tento článek podrobně popisuje, jak pomocí azure cli připojit virtuální pevný disk k jinému virtuálnímu počítači Linux opravit všechny chyby a pak znovu vytvořit původní virtuální počítač. 

## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Zastavte ovlivněný virtuální virtuální ms.
1. Pořiďte snímek z disku operačního systému virtuálního počítače.
1. Vytvořte disk ze snímku disku operačního systému.
1. Připojte a připojte nový disk operačního systému k jinému virtuálnímu počítači s Linuxem pro účely řešení potíží.
1. Připojení k virtuálnímu počítači pro řešení potíží. Upravte soubory nebo spusťte libovolné nástroje k opravě problémů na novém disku operačního systému.
1. Odpojte a odpojte nový disk operačního systému od virtuálního počítače pro řešení potíží.
1. Změňte disk operačního systému pro ovlivněný virtuální modul.

Chcete-li provést tyto kroky řešení potíží, budete potřebovat nejnovější [Azure CLI](/cli/azure/install-az-cli2) nainstalován a přihlášen k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index).

> [!Important]
> Skripty v tomto článku platí jenom pro virtuální počítače, které používají [spravovaný disk](../linux/managed-disks-overview.md). 

V následujících příkladech nahraďte názvy parametrů `myResourceGroup` `myVM`vlastními hodnotami, například a .

## <a name="determine-boot-issues"></a>Určení problémů se spuštěním
Zkontrolujte sériový výstup a zjistěte, proč se váš virtuální počítač nemůže správně spustit. Běžným příkladem je neplatná položka v `/etc/fstab`aplikaci nebo základní virtuální pevný disk, který je odstraněn nebo přesunut.

Získejte zaváděcí protokoly pomocí [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics). Následující příklad získá sériový výstup z `myVM` virtuálního virtuálního `myResourceGroup`zařízení pojmenované ve skupině prostředků s názvem :

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Zkontrolujte sériový výstup a zjistěte, proč se virtuální mu nedaří spustit. Pokud sériový výstup neposkytuje žádné informace, budete muset zkontrolovat `/var/log` soubory protokolu, jakmile budete mít virtuální pevný disk připojený k řešení potíží s virtuálním počítačem.

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Následující příklad zastaví virtuální `myVM` hod pojmenovaný `myResourceGroup`ze skupiny prostředků s názvem :

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Pořízení snímku z disku operačního systému postiženého virtuálního počítače

Snímek je úplná kopie virtuálního pevného disku jen pro čtení. Nelze jej připojit k virtuálnímu virtuálnímu ms. V dalším kroku vytvoříme disk z tohoto snímku. Následující příklad vytvoří snímek `mySnapshot` s názvem z disku operačního systému virtuálního počítače s názvem myVM. 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Vytvoření disku ze snímku

Tento skript vytvoří spravovaný disk s názvem `myOSDisk` ze snímku s názvem `mySnapshot`.  

```azurecli
#Provide the name of your resource group
$resourceGroup=myResourceGroup

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot=mySnapshot

#Provide the name of the Managed Disk
$osDisk=myNewOSDisk

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType=Premium_LRS

#Provide the OS type
$osType=linux

#Provide the name of the virtual machine
$virtualMachine=myVM

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query [id] -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Pokud skupina prostředků a zdrojový snímek není ve stejné oblasti, zobrazí se chyba "Prostředek nebyl nalezen" chyba při spuštění `az disk create`. V takovém případě je `--location <region>` nutné zadat vytvoření disku do stejné oblasti jako zdrojový snímek.

Nyní máte kopii původního disku operačního systému. Tento nový disk můžete připojit k jinému virtuálnímu virtuálnímu počítače se systémem Windows pro účely řešení potíží.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Připojení nového virtuálního pevného disku k jinému virtuálnímu počítači
V následujících několika krocích použijete jiný virtuální virtuální ms pro účely řešení potíží. Připojíte disk k tomuto řešení potíží virtuálního počítače procházet a upravovat obsah disku. Tento proces umožňuje opravit všechny chyby konfigurace nebo zkontrolovat další soubory protokolu aplikace nebo systému.

Tento skript připojit `myNewOSDisk` disk k `MyTroubleshootVM`virtuálnímu počítače :

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $diskId --resource-group MyResourceGroup --size-gb 128 --sku Standard_LRS --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>Připojení připojeného datového disku

> [!NOTE]
> Následující příklady podrobně popisují kroky požadované na virtuálním počítači Ubuntu. Pokud používáte jinou distribuci Linuxu, například Red Hat Enterprise Linux `mount` nebo SUSE, umístění a příkazy souborů protokolu se mohou trochu lišit. Příslušné změny v příkazech naleznete v dokumentaci pro konkrétní distro.

1. SSH k vašemu virtuálnímu počítači pro řešení potíží pomocí příslušných přihlašovacích údajů. Pokud je tento disk prvním datovým diskem připojeným k virtuálnímu `/dev/sdc`počítači pro řešení potíží, je disk pravděpodobně připojen k aplikaci . Slouží `dmesg` k zobrazení připojených disků:

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

    V předchozím příkladu je disk operačního systému na `/dev/sda` a dočasný disk `/dev/sdb`pro každý virtuální počítače je na . Pokud jste měli více datových disků, měly by být na `/dev/sdd`, `/dev/sde`a tak dále.

2. Vytvořte adresář pro připojení stávajícího virtuálního pevného disku. Následující příklad vytvoří adresář `troubleshootingdisk`s názvem :

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Pokud máte na stávajícím virtuálním pevném disku více oddílů, připojte požadovaný oddíl. Následující příklad připojí první primární `/dev/sdc1`oddíl na adrese :

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Osvědčeným postupem je připojení datových disků na virtuálních počítačích v Azure pomocí univerzálně jedinečného identifikátoru (UUID) virtuálního pevného disku. V tomto krátkém scénáři řešení potíží není připojení virtuálního pevného disku pomocí uuid nutné. Při běžném používání však může úpravy `/etc/fstab` pro připojení virtuálních pevných disků pomocí názvu zařízení spíše než UUID způsobit selhání spuštění virtuálního počítače.


## <a name="fix-issues-on-the-new-os-disk"></a>Řešení problémů na novém disku operačního systému
Se stávajícím virtuálním pevným diskem můžete nyní podle potřeby provádět jakékoli kroky údržby a odstraňování potíží. Jakmile vyřešíte problémy, pokračujte následujícími kroky.


## <a name="unmount-and-detach-the-new-os-disk"></a>Odpojení a odpojení nového disku operačního systému
Jakmile jsou chyby vyřešeny, odpojíte a odpojíte existující virtuální pevný disk od virtuálního počítače pro řešení potíží. Virtuální pevný disk nelze použít s žádným jiným virtuálním počítačem, dokud nebude uvolněna zapůjčení připojené virtuální pevný disk k virtuálnímu počítači řešící potíže.

1. Od relace SSH k řešení potíží s virtuálním počítačem odpojte stávající virtuální pevný disk. Nejprve změňte mimo nadřazený adresář pro přípojný bod:

    ```bash
    cd /
    ```

    Nyní odpojte stávající virtuální pevný disk. Následující příklad odpojí zařízení `/dev/sdc1`na adrese :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Nyní odpojte virtuální pevný disk od virtuálního počítače. Ukončete relaci SSH na vašem virtuálním počítači pro řešení potíží:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Změna disku operačního systému pro ohrožený virtuální modul

K výměně disků operačního systému můžete použít azure cli. Není nutné odstranit a znovu vytvořit virtuální ho.

Tento příklad zastaví název `myVM` virtuálního počítače `myNewOSDisk` a přiřadí disk pojmenovaný jako nový disk operačního systému.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>Další kroky
Pokud máte problémy s připojením k virtuálnímu počítači, [přečtěte si článek Poradce při potížích s připojením SSH k virtuálnímu počítači Azure](troubleshoot-ssh-connection.md). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači najdete [v tématu Řešení problémů s připojením aplikací na virtuálním počítači s Linuxem](troubleshoot-app-connection.md).

