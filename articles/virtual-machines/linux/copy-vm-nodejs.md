---
title: Vytvořit kopii virtuálním počítačům s Linuxem pomocí Azure CLI 1.0 | Microsoft Docs
description: Informace o vytvoření kopie virtuálního počítače Azure Linux pomocí Azure CLI 1.0 v modelu nasazení Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: bb350f8d14ad451ad3ff7cd617ca3f90967aaa4b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30906935"
---
# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure-with-the-azure-cli-10"></a>Vytvořit kopii virtuální počítač s Linuxem systémem Azure pomocí Azure CLI 1.0
Tento článek ukazuje, jak vytvořit kopii Azure virtuálního počítače (VM) s Linuxem pomocí modelu nasazení Resource Manager. Nejdřív zkopírujte přes operačního systému a datové disky pro nový kontejner, potom nastavíte síťové prostředky a vytvoření nového virtuálního počítače.

Můžete také [odesílání a vytvořit virtuální počítač z bitové kopie disku vlastní](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- Azure CLI 1.0 – naše rozhraní příkazového řádku pro klasické modely nasazení a modely nasazení správy prostředků (tento článek)
- [Azure CLI 2.0](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků

## <a name="before-you-begin"></a>Než začnete
Ujistěte se, splňují následující předpoklady, než začnete kroky:

* Máte [rozhraní příkazového řádku Azure](../../cli-install-nodejs.md) stažen a nainstalován na váš počítač. 
* Musíte taky některé informace o existující virtuální počítač Azure Linux:

| Informace o zdroji virtuálních počítačů | Kde je lze získat |
| --- | --- |
| název virtuálního počítače |`azure vm list` |
| Název skupiny prostředků |`azure vm list` |
| Umístění |`azure vm list` |
| Název účtu úložiště |`azure storage account list -g <resourceGroup>` |
| Název kontejneru |`azure storage container list -a <sourcestorageaccountname>` |
| Název zdrojového souboru virtuálního pevného disku virtuálního počítače |`azure storage blob list --container <containerName>` |

* Budete muset některé rozhodování o nový virtuální počítač:    <br> -Název kontejneru    <br> -Název virtuálního počítače.    <br> Velikost virtuálního počítače-    <br> Název - vNet    <br> -Název podsítě.    <br> Název - IP    <br> Název - NIC

## <a name="login-and-set-your-subscription"></a>Přihlášení a nastavte předplatné
1. Přihlášení k rozhraní příkazového řádku.

    ```azurecli
    azure login
    ```
2. Ujistěte se, že jste v režimu Resource Manager.

    ```azurecli
    azure config mode arm
    ```
3. Nastavte správné předplatné. Můžete použít "účet azure seznam" zobrazíte všechny z vašich předplatných.

    ```azurecli
    azure account set mySubscriptionID
    ```

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače
Zastavte a navrátit zdrojového virtuálního počítače. 'Seznam virtuálních počítačů azure, můžete použít k získání seznamu všech virtuálních počítačů v předplatného a jejich prostředků názvy skupin.

```azurecli
azure vm stop myResourceGroup myVM
azure vm deallocate myResourceGroup MyVM
```


## <a name="copy-the-vhd"></a>Zkopírujte virtuální pevný disk
Virtuální pevný disk můžete zkopírovat z zdrojového úložiště na cílový pomocí `azure storage blob copy start`. V tomto příkladu přidáme zkopírujte virtuální pevný disk na stejném účtu úložiště, ale jiný kontejner.

Chcete-li kopírovat virtuální pevný disk do jiného kontejneru ve stejném účtu úložiště, zadejte:

```azurecli
azure storage blob copy start \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd \
        myNewContainerName
```

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Nastavení virtuální sítě pro nový virtuální počítač
Nastavení virtuální sítě a síťovou kartu pro nový virtuální počítač. 

```azurecli
azure network vnet create myResourceGroup myVnet -l myLocation

azure network vnet subnet create -a <address.prefix.in.CIDR/format> myResourceGroup myVnet mySubnet

azure network public-ip create myResourceGroup myPublicIP -l myLocation

azure network nic create myResourceGroup myNic -k mySubnet -m myVnet -p myPublicIP -l myLocation
```


## <a name="create-the-new-vm"></a>Vytvoření nového virtuálního počítače
Nyní můžete vytvořit virtuální počítač z nahraný virtuální disk [pomocí šablony správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) nebo prostřednictvím rozhraní příkazového řádku zadáním identifikátor URI pro zkopírovaný disk zadáním:

```azurecli
azure vm create -n myVM -l myLocation -g myResourceGroup -f myNic \
        -z Standard_DS1_v2 -y Linux \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd 
```



## <a name="next-steps"></a>Další postup
Naučte se používat rozhraní příkazového řádku Azure ke správě nového virtuálního počítače, najdete v tématu [rozhraní příkazového řádku Azure pro Azure Resource Manager](../azure-cli-arm-commands.md).

