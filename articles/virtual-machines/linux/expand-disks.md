---
title: Rozbalte virtuální pevné disky na virtuální počítač s Linuxem v Azure | Dokumentace Microsoftu
description: Zjistěte, jak rozšířit virtuální pevné disky na virtuální počítač s Linuxem pomocí Azure CLI
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
ms.openlocfilehash: 0c2d4d1413b6cfd0b5e457e720b59c6c7b575092
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974540"
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Způsob, jak rozbalit virtuální pevné disky na virtuální počítač s Linuxem pomocí Azure CLI

Výchozí velikost virtuálního pevného disku pro operační systém (OS) je obvykle 30 GB na virtuální počítač s Linuxem (VM) v Azure. Je možné [datových disků](add-disk.md) zajistit další úložný prostor, ale můžete také chtít rozšířit stávající datový disk. Tento článek podrobně popisuje, jak rozšířit spravované disky pro virtuální počítač s Linuxem pomocí Azure CLI. 

> [!WARNING]
> Vždy ujistěte se, že můžete zálohovat data před provedením disku měnit velikost operace. Další informace najdete v tématu [zálohování virtuálních počítačů s Linuxem v Azure](tutorial-backup-vms.md).

## <a name="expand-azure-managed-disk"></a>Rozšíření Azure spravovaného disku
Ujistěte se, že máte nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az_login).

Tento článek vyžaduje existující virtuální počítač v Azure s aspoň jedním datovým diskem připojený a připravený. Pokud ještě nemáte virtuální počítač, který můžete použít, přečtěte si téma [vytváření a příprava virtuálního počítače s datovými disky](tutorial-manage-disks.md#create-and-attach-disks).

V následující ukázky nahraďte ukázkové názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad *myResourceGroup* a *myVM*.

1. Operace na virtuálních pevných discích nelze provést s s virtuálním Počítačem. Zrušit přidělení virtuálního počítače s [az vm deallocate](/cli/azure/vm#az_vm_deallocate). V následujícím příkladu se uvolní virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Virtuální počítač musí být uvolněný rozbalte virtuální pevný disk. `az vm stop` neuvolní výpočetní prostředky. Chcete-li uvolnit výpočetní prostředky, použít `az vm deallocate`.

1. Zobrazit seznam spravovaných disků ve skupině prostředků s [az disk list](/cli/azure/disk#az_disk_list). Následující příklad zobrazí seznam spravovaných disků ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Rozbalte vyžaduje disk s [az disk update](/cli/azure/disk#az_disk_update). Následující příklad rozšiřuje spravovaného disku s názvem *myDataDisk* bude *200*velikost Gb:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Při rozšiřování spravovaného disku aktualizované velikosti je mapován na nejbližší velikost spravovaného disku. Tabulka obsahující úrovně a dostupné spravované disky, najdete v části [Azure Přehled služby Managed Disks – ceny a fakturace](../windows/managed-disks-overview.md#pricing-and-billing).

1. Spuštění virtuálního počítače s [az vm start](/cli/azure/vm#az_vm_start). Následující příklad spustí virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-disk-partition-and-filesystem"></a>Rozbalte oddíl disku a systému souborů
Pokud chcete použít rozšířené disku, potřebujete zvětšit základní oddíl a systém souborů.

1. Připojte přes SSH k virtuálnímu počítači s příslušnými přihlašovacími údaji. Můžete získat veřejnou IP adresu vašeho virtuálního počítače s [az vm show](/cli/azure/vm#az_vm_show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Pokud chcete použít rozšířené disku, potřebujete zvětšit základní oddíl a systém souborů.

    a. Pokud již připojen, odpojte disk:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Použití `parted` k zobrazení informací o disku a změnit velikost oddílu:

    ```bash
    sudo parted /dev/sdc
    ```

    Zobrazit informace o existujících rozložení oddílů s `print`. Výstup je podobný následujícím příkladu, který ukazuje, že je základní disk 215 Gb velikost:

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

    c. Rozbalte oddíl s `resizepart`. Zadejte číslo oddílu *1*a velikost pro nový oddíl:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Chcete-li ukončit, zadejte `quit`

1. S oddílem se změněnou velikostí, ověření konzistence oddílu s `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Teď změňte velikost systému souborů s `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Připojte oddíl do požadovaného umístění, jako například `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Chcete-li ověřit, změně velikosti disku s operačním systémem, použijte `df -h`. Následující příklad výstupu ukazuje datovou jednotku */dev/sdc1*, je teď 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Další postup
Pokud potřebujete další úložiště, můžete také [přidat datové disky virtuálního počítače s Linuxem](add-disk.md). Další informace o šifrování disku najdete v tématu [šifrování disků na virtuální počítač s Linuxem pomocí Azure CLI](encrypt-disks.md).
