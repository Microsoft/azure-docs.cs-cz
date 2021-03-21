---
title: Rozbalení virtuálních pevných disků na virtuálním počítači se systémem Linux
description: Naučte se rozbalovat virtuální pevné disky na virtuálním počítači se systémem Linux pomocí Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 72778c431c561f5345dde3d6803e814d6fdebfba
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549120"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Rozbalení virtuálních pevných disků na virtuálním počítači se systémem Linux pomocí Azure CLI

Tento článek popisuje, jak rozbalit spravované disky pro virtuální počítač se systémem Linux pomocí Azure CLI. Můžete [přidat datové disky](add-disk.md) , které poskytují další prostor úložiště, a můžete také rozšířit existující datový disk. Výchozí velikost virtuálního pevného disku pro operační systém (OS) je obvykle 30 GB na virtuálním počítači Linux v Azure. 

> [!WARNING]
> Zajistěte, aby byl systém souborů v dobrém stavu, typ tabulky diskového oddílu bude podporovat novou velikost a před provedením operací změny velikosti disku se ujistěte, že jsou vaše data zálohována. Další informace najdete v tématu [rychlý start Azure Backup](../../backup/quick-backup-vm-portal.md). 

## <a name="expand-an-azure-managed-disk"></a>Rozšíření spravovaného disku Azure
Ujistěte se, že máte nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a že jste k účtu Azure přihlášení pomocí [AZ Login](/cli/azure/reference-index#az-login).

Tento článek vyžaduje existující virtuální počítač v Azure s alespoň jedním připojeným a připraveným datovým diskem. Pokud ještě nemáte virtuální počítač, který můžete použít, přečtěte si téma [Vytvoření a Příprava virtuálního počítače s datovými disky](tutorial-manage-disks.md#create-and-attach-disks).

V následujících ukázkách nahraďte příklady názvů parametrů jako *myResourceGroup* a *myVM* vlastními hodnotami.

1. Operace s virtuálními pevnými disky nejde provést s virtuálním počítačem, na kterém běží. Zrušte přidělení virtuálního počítače pomocí [AZ VM disallocate](/cli/azure/vm#az-vm-deallocate). Následující příklad zruší přidělení virtuálního počítače s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Aby se virtuální pevný disk mohl rozšířit, musí se virtuální počítač uvolnit. Při zastavení virtuálního počítače se `az vm stop` neuvolní výpočetní prostředky. K uvolnění výpočetních prostředků použijte `az vm deallocate` .

1. Zobrazte seznam spravovaných disků ve skupině prostředků pomocí [seznamu AZ disk list](/cli/azure/disk#az-disk-list). Následující příklad zobrazí seznam spravovaných disků ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Požadovaný disk rozbalte pomocí [AZ disk Update](/cli/azure/disk#az-disk-update). V následujícím příkladu se rozšíří spravovaný disk s názvem *myDataDisk* na *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Když rozbalíte spravovaný disk, aktualizovaná velikost se zaokrouhluje na nejbližší velikost spravovaného disku. Tabulku dostupných velikostí a úrovní spravovaných disků najdete v tématu [Přehled Azure Managed disks – ceny a fakturace](../managed-disks-overview.md).

1. Spusťte virtuální počítač pomocí [AZ VM Start](/cli/azure/vm#az-vm-start). Následující příklad spustí virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Rozbalení diskového oddílu a systému souborů
Chcete-li použít rozšířený disk, rozbalte příslušný oddíl a systém souborů.

1. Připojte se k VIRTUÁLNÍmu počítači přes SSH s příslušnými přihlašovacími údaji. Veřejnou IP adresu vašeho virtuálního počítače můžete zobrazit pomocí [AZ VM show](/cli/azure/vm#az-vm-show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --output tsv
    ```

1. Rozbalte příslušný oddíl a systém souborů.

    a. Pokud je disk už připojený, odpojte ho:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Použijte `parted` k zobrazení informací o disku a změně velikosti oddílu:

    ```bash
    sudo parted /dev/sdc
    ```

    Zobrazit informace o existujícím rozložení oddílu pomocí `print` . Výstup je podobný následujícímu příkladu, který ukazuje, že základní disk je 215 GB:

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

    c. Rozbalte oddíl s `resizepart` . Zadejte číslo oddílu, *1* a velikost nového oddílu:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Chcete-li skončit, zadejte `quit` .

1. Při změně velikosti oddílu Ověřte konzistenci oddílu pomocí `e2fsck` :

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Změnit velikost systému souborů na `resize2fs` :

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Připojte oddíl k požadovanému umístění, například `/datadrive` :

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Chcete-li ověřit, zda došlo ke změně velikosti datového disku, použijte `df -h` . Následující příklad výstupu ukazuje, že datová jednotka */dev/sdc1* je teď 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Další kroky
* Pokud potřebujete další úložiště, můžete také [přidat datové disky do virtuálního počítače se systémem Linux](add-disk.md). 
* Další informace o šifrování disku najdete v tématu [Azure Disk Encryption pro virtuální počítače se systémem Linux](disk-encryption-overview.md).
