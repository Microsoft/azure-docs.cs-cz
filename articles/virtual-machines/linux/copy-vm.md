---
title: Kopírování virtuálního počítače se systémem Linux pomocí Azure CLI
description: Naučte se vytvořit kopii virtuálního počítače Azure Linux pomocí Azure CLI a Managed Disks.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.openlocfilehash: 7f9ac0ab9eacb90bde70c85ea06bc19a18aa0c05
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201140"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Vytvoření kopie virtuálního počítače se systémem Linux pomocí rozhraní příkazového řádku Azure a Managed Disks

V tomto článku se dozvíte, jak vytvořit kopii virtuálního počítače Azure s operačním systémem Linux pomocí rozhraní příkazového řádku Azure CLI. Pokud chcete kopírovat, vytvářet, ukládat a sdílet image virtuálních počítačů ve velkém měřítku, přečtěte si téma [Galerie sdílených imagí](../shared-images-cli.md).

[Virtuální počítač můžete také nahrát a vytvořit z virtuálního pevného disku](upload-vhd.md).

## <a name="prerequisites"></a>Požadavky

-   Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-az-cli2).

-   Přihlaste se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index#az-login).

-   Mít virtuální počítač Azure, který se použije jako zdroj pro vaši kopii.

## <a name="stop-the-source-vm"></a>Zastavení zdrojového virtuálního počítače

Nasaďte zdrojový virtuální počítač pomocí [AZ VM disallocate](/cli/azure/vm#az-vm-deallocate).
Následující příklad zruší přidělení virtuálního počítače s názvem *myVM* ve skupině prostředků *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Kopírování zdrojového virtuálního počítače

K zkopírování virtuálního počítače vytvoříte kopii základního virtuálního pevného disku. Tento proces vytvoří specializovaný virtuální pevný disk (VHD) jako spravovaný disk, který obsahuje stejnou konfiguraci a nastavení jako zdrojový virtuální počítač.

Další informace o Spravovaných discích Azure najdete v tématu [Přehled Spravovaných disků Azure](../managed-disks-overview.md). 

1.  Seznamte se s každým virtuálním počítačem a názvem jeho disku s operačním systémem pomocí [seznamu AZ VM list](/cli/azure/vm#az-vm-list). Následující příklad zobrazí seznam všech virtuálních počítačů ve skupině prostředků s názvem *myResourceGroup*:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    Výstup se podobá následujícímu příkladu:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Zkopírujte disk vytvořením nového spravovaného disku a pomocí [AZ disk Create](/cli/azure/disk#az-disk-create). Následující příklad vytvoří disk s názvem *myCopiedDisk* ze spravovaného disku s názvem *myDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Ověřte, že se spravované disky teď nacházejí ve vaší skupině prostředků, pomocí [AZ disk list](/cli/azure/disk#az-disk-list). Následující příklad vypíše spravované disky ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Nastavení virtuální sítě

V následujících volitelných krocích se vytvoří nová virtuální síť, podsíť, veřejná IP adresa a karta virtuální sítě (NIC).

Pokud kopírujete virtuální počítač pro účely řešení potíží nebo další nasazení, možná nebudete chtít použít virtuální počítač v existující virtuální síti.

Pokud chcete vytvořit infrastrukturu virtuální sítě pro zkopírované virtuální počítače, postupujte podle následujících několika kroků. Pokud nechcete vytvořit virtuální síť, přejděte k [Vytvoření virtuálního počítače](#create-a-vm).

1.  Vytvořte virtuální síť pomocí [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create). Následující příklad vytvoří virtuální síť s názvem *myVnet* a podsíť s názvem *mySubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Vytvořte veřejnou IP adresu pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create). Následující příklad vytvoří veřejnou IP adresu s názvem *myPublicIP* s názvem DNS *mypublicdns*. (Protože název DNS musí být jedinečný, zadejte jedinečný název.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Pomocí [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create)vytvořte síťovou kartu.
    Následující příklad vytvoří síťovou kartu s názvem *myNic* , která je připojena k podsíti *mySubnet* :

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Pomocí [AZ VM Create](/cli/azure/vm#az-vm-create)vytvořte virtuální počítač.

Určete zkopírovaný spravovaný disk, který se použije jako disk s operačním systémem ( `--attach-os-disk` ), a to následujícím způsobem:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Další kroky

Naučte se používat [sdílenou galerii imagí](../shared-images-cli.md) ke správě imagí virtuálních počítačů.
