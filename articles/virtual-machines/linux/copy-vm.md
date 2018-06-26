---
title: Kopírování virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se vytvořit kopii virtuálního počítače Azure Linux pomocí příkazového řádku Azure CLI a spravované disky.
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
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 8d250f1289c3757d5ea862a1c195dde6f8efb0eb
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938260"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Vytvoří kopii virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure a spravovat disky


Tento článek ukazuje, jak vytvořit kopii Azure virtuálního počítače (VM) s Linuxem pomocí Azure CLI 2.0 a modelu nasazení Azure Resource Manager. 

Můžete také [odesílání a vytvoření virtuálního počítače z disku VHD](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Požadavky


-   Nainstalujte [rozhraní příkazového řádku Azure 2.0](/cli/azure/install-az-cli2)

-   Přihlaste se k účtu Azure s [az přihlášení](/cli/azure/reference-index#az_login).

-   Máte virtuální počítač Azure má použít jako zdroj pro vaší kopie.

## <a name="step-1-stop-the-source-vm"></a>Krok 1: Zastavit zdrojového virtuálního počítače


Deallocate zdrojového virtuálního počítače pomocí [az OM deallocate](/cli/azure/vm#az_vm_deallocate).
Následující příklad zruší přidělení virtuálního počítače s názvem **Můjvp** ve skupině prostředků **myResourceGroup**:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Krok 2: Kopírování zdrojového virtuálního počítače


Pokud chcete zkopírovat virtuální počítač, vytvořit kopii základní virtuální pevný disk. Tento proces vytvoří specializované virtuálního pevného disku jako spravované Disk, který obsahuje stejnou konfiguraci a nastavení jako zdrojový virtuální počítač.

Další informace o službě Azure Managed Disks najdete v tématu [Přehled služby Azure Managed Disks](../windows/managed-disks-overview.md). 

1.  Seznam jednotlivé virtuální počítače a název jeho operačního systému na disku s [seznamu virtuálních počítačů az](/cli/azure/vm#az_vm_list). Následující příklad vypíše všechny virtuální počítače ve skupině prostředků s názvem **myResourceGroup**:
    
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

1.  Kopie disku tak, že vytvoříte novou spravovat pomocí disku [vytvoření disku az](/cli/azure/disk#az_disk_create). Následující příklad vytvoří disk s názvem **myCopiedDisk** ze spravovaných disk s názvem **myDisk**:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Ověřit spravované disky nyní ve vaší skupině prostředků pomocí [seznam disků az](/cli/azure/disk#az_disk_list). Následující příklad vypíše disky spravované ve skupině prostředků s názvem **myResourceGroup**:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>Krok 3: Nastavení virtuální sítě


Následující volitelný postup vytvoření nové virtuální sítě, podsítě, veřejnou IP adresu a virtuální síťová karta (NIC).

Pokud kopírujete virtuální počítač pro řešení potíží s účely nebo další nasazení, nemusí chcete použít virtuální počítač v existující virtuální síť.

Pokud chcete vytvořit virtuální síťovou infrastrukturu pro zkopírovaný virtuální počítače, postupujte podle několika dalších krocích. Pokud nechcete vytvořit virtuální síť, přejděte k [krok 4: vytvoření virtuálního počítače](#step-4-create-a-vm).

1.  Vytvoření virtuální sítě pomocí [vytvoření sítě vnet az](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální síť s názvem **myVnet** a podsíť s názvem **mySubnet**:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Vytvoření veřejné IP adresy pomocí [vytvoření veřejné sítě az-ip](/cli/azure/network/public-ip#az_network_public_ip_create). Následující příklad vytvoří veřejnou IP adresu s názvem **myPublicIP** s názvem DNS **mypublicdns**. (Název DNS musí být jedinečný, takže zadejte jedinečný název.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Vytvořte pomocí seskupování [vytvořit síťových adaptérů sítě az](/cli/azure/network/nic#az_network_nic_create).
    Následující příklad vytvoří síťový adaptér s názvem **myNic** připojená k **mySubnet** podsítě:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>Krok 4: Vytvoření virtuálního počítače

Nyní můžete vytvořit virtuální počítač pomocí [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create).

Zadejte zkopírovaný spravované disk, který chcete použít jako disk operačního systému (--připojit disk operačního systému), a to takto:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Další postup

Naučte se používat rozhraní příkazového řádku Azure ke správě nový virtuální počítač, najdete v tématu [rozhraní příkazového řádku Azure pro Azure Resource Manager](../azure-cli-arm-commands.md).
