---
title: Kopírování virtuálního počítače s Linuxem pomocí azure cli
description: Zjistěte, jak vytvořit kopii virtuálního počítače Azure Linux pomocí Azure CLI a spravovaných disků.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: ed8574133eafe751699e90ea8cae832ee649fb00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969584"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Vytvoření kopie virtuálního počítače s Linuxem pomocí Azure CLI a spravovaných disků

Tento článek ukazuje, jak vytvořit kopii virtuálního počítače Azure (VM) se systémem Linux pomocí Azure CLI a modelu nasazení Azure Resource Manager. 

Můžete taky [nahrát a vytvořit virtuální hod z virtuálního pevného disku](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Požadavky

-   Nainstalujte [rozhraní příkazového řádku Azure CLI](/cli/azure/install-az-cli2).

-   Přihlaste se k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az-login).

-   Mít virtuální počítač Azure použít jako zdroj pro vaši kopii.

## <a name="stop-the-source-vm"></a>Zastavení zdrojového virtuálního virtuálního montovana

Navrátit zdrojový virtuální virtuální hod pomocí [az vm navrátit](/cli/azure/vm#az-vm-deallocate).
Následující příklad navrací virtuální hosti s názvem *myVM* ve skupině prostředků *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Kopírování zdrojového virtuálního virtuálního montova.

Chcete-li zkopírovat virtuální počítač, vytvořte kopii podkladového virtuálního pevného disku. Tento proces vytvoří specializovaný virtuální pevný disk (VHD) jako spravovaný disk, který obsahuje stejnou konfiguraci a nastavení jako zdrojový virtuální počítač.

Další informace o Spravovaných discích Azure najdete v tématu [Přehled Spravovaných disků Azure](../windows/managed-disks-overview.md). 

1.  Seznam jednotlivých virtuálních počítačů a název jeho disku operačního systému se [seznamem vm az](/cli/azure/vm#az-vm-list). V následujícím příkladu jsou uvedeny všechny virtuální společnosti ve skupině prostředků s názvem *myResourceGroup*:
    
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

1.  Zkopírujte disk vytvořením nového spravovaného disku a pomocí [az disku create](/cli/azure/disk#az-disk-create). Následující příklad vytvoří disk s názvem *myCopiedDisk* ze spravovaného disku s názvem *myDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Ověřte spravované disky ve skupině prostředků pomocí [seznamu az disků](/cli/azure/disk#az-disk-list). V následujícím příkladu jsou uvedeny spravované disky ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Nastavení virtuální sítě

Následující volitelné kroky vytvoří novou virtuální síť, podsíť, veřejnou IP adresu a kartu rozhraní virtuální sítě (NIC).

Pokud kopírujete virtuální počítač pro účely řešení potíží nebo další nasazení, možná nebudete chtít použít virtuální počítač v existující virtuální síti.

Pokud chcete vytvořit infrastrukturu virtuální sítě pro zkopírované virtuální počítače, postupujte podle následujících několika kroků. Pokud nechcete vytvořit virtuální síť, přeskočte na [Vytvořit virtuální počítač](#create-a-vm).

1.  Vytvořte virtuální síť pomocí [az sítě virtuální sítě vytvořit](/cli/azure/network/vnet#az-network-vnet-create). Následující příklad vytvoří virtuální síť s názvem *myVnet* a podsíť s názvem *mySubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Vytvořte veřejnou IP adresu pomocí [az sítě public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). Následující příklad vytvoří veřejnou IP adresu s názvem *myPublicIP* s názvem DNS *mypublicdns*. (Vzhledem k tomu, že název DNS musí být jedinečný, zadejte jedinečný název.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Vytvořte síťovou konto pomocí [az network nic create](/cli/azure/network/nic#az-network-nic-create).
    Následující příklad vytvoří síťovou síť ovou síť s názvem *myNic,* která je připojena k podsíti *mySubnet:*

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální hospo- vytvoření pomocí [az vm create](/cli/azure/vm#az-vm-create).

Zadejte zkopírovaný spravovaný disk, který se má použít jako disk operačního systému (`--attach-os-disk`), následujícím způsobem:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět, jak používat [sdílenou galerii obrázků](shared-images.md) ke správě imblin virtuálních počítačů.
