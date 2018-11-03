---
title: Kopírování virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit kopii tohoto virtuálního počítače s Linuxem Azure pomocí rozhraní příkazového řádku Azure a spravované disky.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: a9caaaa0dfe402339ba01be899073bb17de15906
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962040"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Vytvoření kopie virtuálního počítače s Linuxem pomocí Azure CLI a Managed Disks

Tento článek ukazuje, jak vytvořit kopii váš virtuální počítač Azure (VM) s Linuxem pomocí rozhraní příkazového řádku Azure a modelu nasazení Azure Resource Manageru. 

Můžete také [nahrání a vytvoření virtuálního počítače z virtuálního pevného disku](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Požadavky

-   Nainstalujte [rozhraní příkazového řádku Azure CLI](/cli/azure/install-az-cli2).

-   Přihlaste se k účtu Azure pomocí [az login](/cli/azure/reference-index#az-login).

-   Mají použít jako zdroj pro vaši kopii virtuálního počítače Azure.

## <a name="stop-the-source-vm"></a>Zastavit zdrojový virtuální počítač

Uvolnit zdrojový virtuální počítač s použitím [az vm deallocate](/cli/azure/vm#az-vm-deallocate).
V následujícím příkladu se uvolní virtuální počítač s názvem *myVM* ve skupině prostředků *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Zkopírujte zdrojový virtuální počítač

K vytvoření kopie virtuálního počítače, vytvořte kopii základní virtuální pevný disk. Tento proces vytvoří specializované virtuální pevný disk (VHD) jako spravovaný Disk, který obsahuje stejné konfigurace a nastavení jako zdrojový virtuální počítač.

Další informace o službě Azure Managed Disks najdete v tématu [Přehled služby Azure Managed Disks](../windows/managed-disks-overview.md). 

1.  Seznam všech virtuálních počítačů a název jeho operační systém na disku s [az vm list](/cli/azure/vm#az-vm-list). Následující příklad zobrazí seznam všech virtuálních počítačů ve skupině prostředků s názvem *myResourceGroup*:
    
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

1.  Zkopírujte disk vytvořením nového spravovaného disku a pomocí [az disk vytvořit](/cli/azure/disk#az-disk-create). Následující příklad vytvoří disk s názvem *myCopiedDisk* ze spravovaného disku s názvem *myDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Ověřte spravované disky, nyní ve vaší skupině prostředků s použitím [az disk list](/cli/azure/disk#az-disk-list). Následující příklad vypíše spravované disky ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Nastavit virtual network

Následující volitelné kroky vytvoření nové virtuální sítě, podsítě, veřejné IP adresy a virtuální síťová karta (NIC).

Pokud kopírujete virtuální počítač pro řešení potíží s účely nebo další nasazení, možná chcete použít virtuální počítač v existující virtuální sítě.

Pokud chcete vytvořit virtuální síťovou infrastrukturu pro kopírované virtuální počítače, postupujte podle několik dalších kroků. Pokud nechcete vytvořit virtuální síť, přejděte k [vytvoření virtuálního počítače](#create-a-vm).

1.  Vytvoření virtuální sítě s použitím [az network vnet vytvořit](/cli/azure/network/vnet#az-network-vnet-create). Následující příklad vytvoří virtuální síť s názvem *myVnet* a podsíť s názvem *mySubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Vytvoření veřejné IP adresy s použitím [az network public-ip vytvořit](/cli/azure/network/public-ip#az-network-public-ip-create). Následující příklad vytvoří veřejnou IP adresu s názvem *myPublicIP* s názvem služby DNS *mypublicdns*. (Protože název DNS musí být jedinečný, zadejte jedinečný název.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Vytvořte síťové rozhraní s využitím [az network nic vytvořit](/cli/azure/network/nic#az-network-nic-create).
    Následující příklad vytvoří síťové rozhraní s názvem *myNic* , který je připojen k *mySubnet* podsítě:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače pomocí [az vm vytvořit](/cli/azure/vm#az-vm-create).

Zadejte zkopírovaný spravovaný disk určený jako disk s operačním systémem (`--attach-os-disk`), následujícím způsobem:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Další postup

Další informace o použití Azure CLI ke správě vašeho nového virtuálního počítače, naleznete v tématu [příkazy rozhraní příkazového řádku Azure pro Azure Resource Manageru](../azure-cli-arm-commands.md).
