---
title: Převod virtuálního počítače Linux z nespravovaných disků na Managed disks
description: Jak převést virtuální počítač Linux z nespravovaných disků na Managed disks pomocí Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 89035d26e05ca4b93dc271f02fef1afea24b0f20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88815330"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Převod virtuálního počítače se systémem Linux z nespravovaných disků na Managed disks

Pokud máte existující virtuální počítače s Linuxem, které používají nespravované disky, můžete virtuální počítače převést na používání [Azure Managed disks](../managed-disks-overview.md). Tento proces převede disk s operačním systémem i všechny připojené datové disky.

V tomto článku se dozvíte, jak převést virtuální počítače pomocí Azure CLI. Pokud ho potřebujete nainstalovat nebo upgradovat, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Než začnete
* Přečtěte si [Nejčastější dotazy týkající se migrace na Managed disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Původní virtuální pevné disky a účet úložiště používané virtuálním počítačem před převodem se neodstraní. Budou se vám za ně i nadále účtovat poplatky. Abyste se vyhnuli účtování poplatků za tyto artefakty, po ověření dokončení převodu odstraňte původní objekty blob virtuálních pevných disků. Pokud potřebujete najít tyto nepřipojené disky a odstranit je, přečtěte si náš článek [hledání a odstraňování nepřipojených spravovaných a nespravovaných disků Azure](find-unattached-disks.md).

## <a name="convert-single-instance-vms"></a>Převod virtuálních počítačů s jednou instancí
Tato část popisuje, jak převést virtuální počítače Azure s jednou instancí z nespravovaných disků na Managed disks. (Pokud jsou vaše virtuální počítače ve skupině dostupnosti, přečtěte si další část.) Tento proces můžete použít k převodu virtuálních počítačů z nespravovaných disků úrovně Premium (SSD) na služby Premium Managed disks nebo z nespravovaných disků Standard (HDD) na úrovně Standard Managed disks.

1. Zrušení přidělení virtuálního počítače pomocí [AZ VM disallocate](/cli/azure/vm). Následující příklad zruší přidělení virtuálního počítače `myVM` ve skupině prostředků s názvem `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Pomocí [AZ VM Convert](/cli/azure/vm)PŘEVEĎTE virtuální počítač na spravované disky. Následující proces převede virtuální počítač s názvem `myVM` , včetně disku s operačním systémem a všech datových disků:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Po převodu na spravované disky spusťte virtuální počítač pomocí [AZ VM Start](/cli/azure/vm). V následujícím příkladu se spustí virtuální počítač `myVM` ve skupině prostředků s názvem `myResourceGroup` .

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Převod virtuálních počítačů ve skupině dostupnosti

Pokud jsou virtuální počítače, které chcete převést na spravované disky, ve skupině dostupnosti, musíte nejdřív převést skupinu dostupnosti na spravovanou skupinu dostupnosti.

Před převodem skupiny dostupnosti je nutné uvolnit všechny virtuální počítače ve skupině dostupnosti. Naplánujte převod všech virtuálních počítačů na spravované disky po převedení samotné skupiny dostupnosti na spravovanou skupinu dostupnosti. Pak spusťte všechny virtuální počítače a pokračujte v provozu jako normálně.

1. Vypíše seznam všech virtuálních počítačů ve skupině dostupnosti pomocí výrazu [AZ VM Availability-set list](/cli/azure/vm/availability-set). V následujícím příkladu jsou uvedené všechny virtuální počítače ve skupině dostupnosti s názvem `myAvailabilitySet` ve skupině prostředků s názvem `myResourceGroup` :

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Zrušením přidělení všech virtuálních počítačů pomocí [AZ VM disallocate](/cli/azure/vm). Následující příklad zruší přidělení virtuálního počítače `myVM` ve skupině prostředků s názvem `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Pomocí [AZ VM Availability-set Convert](/cli/azure/vm/availability-set)převeďte skupinu dostupnosti. Následující příklad převede skupinu dostupnosti s názvem `myAvailabilitySet` ve skupině prostředků s názvem `myResourceGroup` :

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Pomocí [AZ VM Convert](/cli/azure/vm)proveďte převod všech virtuálních počítačů na spravované disky. Následující proces převede virtuální počítač s názvem `myVM` , včetně disku s operačním systémem a všech datových disků:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Po převodu na spravované disky spusťte všechny virtuální počítače pomocí [AZ VM Start](/cli/azure/vm). V následujícím příkladu se spustí virtuální počítač `myVM` ve skupině prostředků s názvem `myResourceGroup` :

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Převést pomocí Azure Portal

Nespravované disky můžete také převést na spravované disky pomocí Azure Portal.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Vyberte virtuální počítač ze seznamu virtuálních počítačů na portálu.
3. V okně pro virtuální počítač vyberte z nabídky **disky** .
4. V horní části okna **disky** vyberte **migrovat na Managed disks**.
5. Pokud je váš virtuální počítač ve skupině dostupnosti, v okně **migrovat do spravovaných disků** se zobrazí upozornění, že nejdřív potřebujete skupinu dostupnosti převést. Upozornění by mělo mít odkaz, který můžete kliknutím převést na skupinu dostupnosti. Po převedení skupiny dostupnosti nebo pokud váš virtuální počítač není ve skupině dostupnosti, klikněte na **migrovat** a zahajte proces migrace disků na spravované disky.

Po dokončení migrace se virtuální počítač zastaví a restartuje.

## <a name="next-steps"></a>Další kroky

Další informace o možnostech úložiště najdete v tématu [Přehled Azure Managed disks](../managed-disks-overview.md).
