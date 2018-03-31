---
title: Rozbalte virtuální pevné disky na virtuální počítač s Linuxem v Azure | Microsoft Docs
description: Zjistěte, jak rozšířit virtuální pevné disky na virtuální počítač s Linuxem pomocí Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: rogarana
ms.openlocfilehash: c3dcd2f9c71b64ac48c2c7b6f51d2694d649c15e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Způsob, jak rozbalit virtuální pevné disky na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure
Výchozí velikost virtuálního pevného disku pro operační systém (OS) je obvykle 30 GB na virtuální počítač s Linuxem (VM) v Azure. Můžete [přidat datových disků](add-disk.md) zajistit pro dalšího volného místa, ale můžete také chtít rozšířit stávající datový disk. Tento článek podrobně popisují rozbalte spravované disky pro virtuální počítač s Linuxem pomocí Azure CLI 2.0. Můžete také rozšířit nespravované disk operačního systému pomocí [Azure CLI 1.0](expand-disks-nodejs.md).

> [!WARNING]
> Ujistěte se, můžete zálohovat data před provedením disku změnit velikost operace vždy. Další informace najdete v tématu [zálohovat virtuální počítače s Linuxem v Azure](tutorial-backup-vms.md).

## <a name="expand-azure-managed-disk"></a>Rozbalte spravovaných disků na Azure
Ujistěte se, že máte nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login).

Tento článek vyžaduje existující virtuální počítač v Azure s alespoň jeden datový disk připojený a připravený. Pokud jste ještě není virtuální počítač, který můžete použít, najdete v části [vytvořit a připravit virtuální počítač s datovými disky](tutorial-manage-disks.md#create-and-attach-disks).

V následující ukázky nahraďte názvy parametrů příklad vlastní hodnoty. Zahrnout názvy parametrů příklad *myResourceGroup* a *Můjvp*.

1. Operace na virtuálních pevných discích nelze provést s virtuální počítač spuštěn. Zrušit přidělení virtuálního počítače s [az OM deallocate](/cli/azure/vm#az_vm_deallocate). Následující příklad zruší přidělení virtuálního počítače s názvem *Můjvp* ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Virtuální počítač musí být navrácena rozbalte virtuální pevný disk. `az vm stop` neuvolní výpočetní prostředky. Chcete-li uvolnit výpočetní prostředky, pomocí `az vm deallocate`.

2. Zobrazit seznam spravovaných disků ve skupině prostředků s [seznam disků az](/cli/azure/disk#az_disk_list). Tento příklad zobrazuje seznam spravovaných disků ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Rozbalte požadované disk s [az disku aktualizace](/cli/azure/disk#az_disk_update). Následující příklad rozšíří spravované disk s názvem *myDataDisk* být *200*Gb velikost:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Když rozšiřujete se spravovaným diskem, aktualizované velikost je namapována na nejbližší velikost spravovaného disku. Tabulku spravovaných disků na dostupné velikosti a vrstvy, najdete v části [spravované disky přehled Azure – ceny a fakturace](../windows/managed-disks-overview.md#pricing-and-billing).

3. Spuštění virtuálního počítače s [spuštění virtuálního počítače az](/cli/azure/vm#az_vm_start). Následující příklad spustí virtuální počítač s názvem *Můjvp* ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-disk-partition-and-filesystem"></a>Rozbalte diskový oddíl a systému souborů
Chcete-li použít rozšířené disk, rozbalte položku Základní oddílu a systému souborů.

1. SSH pro virtuální počítač s příslušnými přihlašovacími údaji. Můžete získat veřejnou IP adresu vašeho virtuálního počítače s [az virtuálních počítačů zobrazit](/cli/azure/vm#az_vm_show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

2. Chcete-li použít rozšířené disk, rozbalte položku Základní oddílu a systému souborů.

    a. Pokud je již připojen, odpojte disk:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Použití `parted` k zobrazení informací o disku a změňte velikost oddílu:

    ```bash
    sudo parted /dev/sdc
    ```

    Zobrazení informací o existující rozložení oddílů s `print`. Výstup bude vypadat v následujícím příkladu, který ukazuje, že disku, na kterém je 215 Gb velikost:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Rozbalte oddíl s `resizepart`. Zadejte číslo oddílu *1*a velikost nového oddílu:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Chcete-li ukončit, zadejte `quit`

3. S oddílem po změně velikosti, ověření konzistence oddílu s `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

4. Nyní změnit velikost souborů s `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

5. Připojit oddíl do požadovaného umístění, jako například `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

6. Pokud chcete ověřit, změně velikosti disku operačního systému, použijte `df -h`. Následující příklad výstupu zobrazuje datová jednotka */dev/sdc1*, je nyní 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Další postup
Pokud potřebujete další úložiště, můžete také [přidat datových disků pro virtuální počítač s Linuxem](add-disk.md). Další informace o šifrování disku najdete v tématu [šifrování disky na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure](encrypt-disks.md).
