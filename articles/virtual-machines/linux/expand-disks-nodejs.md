---
title: Rozbalte disk operačního systému na virtuální počítač s Linuxem pomocí Azure CLI 1.0 | Microsoft Docs
description: Zjistěte, jak rozšířit virtuální disk operačního systému (OS) na virtuální počítač s Linuxem pomocí Azure CLI 1.0 a modelu nasazení Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: f81054727bb1f0e8ffa752783e866a72d573589d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Rozbalte disk operačního systému na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure s Azure CLI 1.0
Výchozí velikost virtuálního pevného disku pro operační systém (OS) je obvykle 30 GB na virtuální počítač s Linuxem (VM) v Azure. Můžete [přidat datových disků](add-disk.md) zajistit pro dalšího volného místa, ale můžete také chtít rozbalte disk operačního systému. Tento článek podrobně popisují rozbalte disk operačního systému pro virtuální počítač s Linuxem pomocí Azure CLI 1.0 nespravované disky.

## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#prerequisites) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](expand-disks.md) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků

## <a name="prerequisites"></a>Požadavky
Je nutné [nejnovější Azure CLI 1.0](../../cli-install-nodejs.md) nainstalován a přihlášení k [účet Azure](https://azure.microsoft.com/pricing/free-trial/) pomocí režimu Resource Manager takto:

```azurecli
azure config mode arm
```

V následující ukázky nahraďte názvy parametrů příklad vlastní hodnoty. Zahrnout názvy parametrů příklad *myResourceGroup* a *Můjvp*.

## <a name="expand-os-disk"></a>Rozbalení disku operačního systému

1. Operace na virtuálních pevných discích nelze provést s virtuální počítač spuštěn. V následujícím příkladu se zastaví a zruší přidělení virtuálního počítače s názvem *Můjvp* ve skupině prostředků s názvem *myResourceGroup*:

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop` neuvolní výpočetní prostředky. Chcete-li uvolnit výpočetní prostředky, pomocí `azure vm deallocate`. Virtuální počítač musí být navrácena rozbalte virtuální pevný disk.

2. Aktualizovat velikost nespravované disk operačního systému pomocí `azure vm set` příkaz. Následující příklad aktualizace virtuálního počítače s názvem *Můjvp* ve skupině prostředků s názvem *myResourceGroup* být *50* GB:

    ```azurecli
    azure vm set \
        --resource-group myResourceGroup \
        --name myVM \
        --new-os-disk-size 50
    ```

3. Spusťte virtuální počítač následujícím způsobem:

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH pro virtuální počítač s příslušnými přihlašovacími údaji. Pokud chcete ověřit, změně velikosti disku operačního systému, použijte `df -h`. Následující příklad výstupu zobrazuje primární oddíl (*/dev/sda1*) je nyní 50 GB:

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>Další postup
Pokud potřebujete další úložiště, můžete také [přidat datových disků pro virtuální počítač s Linuxem](add-disk.md). Další informace o šifrování disku najdete v tématu [šifrování disky na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure](encrypt-disks.md).
