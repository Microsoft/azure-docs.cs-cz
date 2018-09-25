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
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 64b33fcd25582f6b1d3e7efe12aba85bb17c4cca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951198"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Vytvoření kopie virtuálního počítače s Linuxem pomocí Azure CLI a Managed Disks

Tento článek ukazuje, jak vytvořit kopii váš virtuální počítač Azure (VM) s Linuxem pomocí rozhraní příkazového řádku Azure a modelu nasazení Azure Resource Manageru. 

Můžete také [nahrání a vytvoření virtuálního počítače z virtuálního pevného disku](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Požadavky


-   Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2)

-   Přihlaste se k účtu Azure pomocí [az login](/cli/azure/reference-index#az_login).

-   Mají použít jako zdroj pro vaši kopii virtuálního počítače Azure.

## <a name="step-1-stop-the-source-vm"></a>Krok 1: Zastavte zdrojový virtuální počítač


Uvolnit zdrojový virtuální počítač s použitím [az vm deallocate](/cli/azure/vm#az_vm_deallocate).
V následujícím příkladu se uvolní virtuální počítač s názvem **myVM** ve skupině prostředků **myResourceGroup**:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Krok 2: Kopírování zdrojový virtuální počítač


K vytvoření kopie virtuálního počítače, vytvořte kopii základní virtuální pevný disk. Tento proces vytvoří specializovaného VHD jako spravovaný Disk, který obsahuje stejné konfigurace a nastavení jako zdrojový virtuální počítač.

Další informace o službě Azure Managed Disks najdete v tématu [Přehled služby Azure Managed Disks](../windows/managed-disks-overview.md). 

1.  Seznam všech virtuálních počítačů a název jeho operační systém na disku s [az vm list](/cli/azure/vm#az_vm_list). Následující příklad zobrazí seznam všech virtuálních počítačů ve skupině prostředků s názvem **myResourceGroup**:
    
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

1.  Kopírování disku tak, že vytvoříte nový spravovaných disků pomocí [az disk vytvořit](/cli/azure/disk#az_disk_create). Následující příklad vytvoří disk s názvem **myCopiedDisk** ze spravovaného disku s názvem **myDisk**:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Ověřte spravované disky, nyní ve vaší skupině prostředků s použitím [az disk list](/cli/azure/disk#az_disk_list). Následující příklad vypíše spravované disky ve skupině prostředků s názvem **myResourceGroup**:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>Krok 3: Nastavení virtuální sítě


Následující volitelné kroky vytvoření nové virtuální sítě, podsítě, veřejné IP adresy a virtuální síťová karta (NIC).

Pokud kopírujete virtuální počítač pro řešení potíží s účely nebo další nasazení, možná chcete použít virtuální počítač v existující virtuální sítě.

Pokud chcete vytvořit virtuální síťovou infrastrukturu pro kopírované virtuální počítače, postupujte podle několik dalších kroků. Pokud nechcete vytvořit virtuální síť, přejděte k [krok 4: vytvoření virtuálního počítače](#step-4-create-a-vm).

1.  Vytvoření virtuální sítě s použitím [az network vnet vytvořit](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální síť s názvem **myVnet** a podsíť s názvem **mySubnet**:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Vytvoření veřejné IP adresy s použitím [az network public-ip vytvořit](/cli/azure/network/public-ip#az_network_public_ip_create). Následující příklad vytvoří veřejnou IP adresu s názvem **myPublicIP** s názvem služby DNS **mypublicdns**. (Název DNS musí být jedinečné, proto zadejte jedinečný název.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Vytvořte síťové rozhraní pomocí [az network nic vytvořit](/cli/azure/network/nic#az_network_nic_create).
    Následující příklad vytvoří síťové rozhraní s názvem **myNic** , který je připojen k **mySubnet** podsítě:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>Krok 4: Vytvoření virtuálního počítače

Nyní můžete vytvořit virtuální počítač s použitím [az vm vytvořit](/cli/azure/vm#az_vm_create).

Zadejte zkopírovaný spravovaný disk určený jako disk s operačním systémem (– připojení disku operačního systému), následujícím způsobem:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Další postup

Další informace o použití Azure CLI ke správě vašeho nového virtuálního počítače, naleznete v tématu [příkazy rozhraní příkazového řádku Azure pro Azure Resource Manageru](../azure-cli-arm-commands.md).
