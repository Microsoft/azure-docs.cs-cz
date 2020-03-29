---
title: Převod virtuálního počítače s Linuxem z nespravovaných disků na spravované disky
description: Jak převést virtuální počítač s Linuxem z nespravovaných disků na spravované disky pomocí azure cli.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2774dcbd5fc5b01627b965c2c02d870412c8bf77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969692"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Převod virtuálního počítače s Linuxem z nespravovaných disků na spravované disky

Pokud máte existující virtuální počítače s Linuxem, které používají nespravované disky, můžete virtuální počítače převést na [spravované disky Azure](../linux/managed-disks-overview.md). Tento proces převádí disk operačního systému i všechny připojené datové disky.

Tento článek ukazuje, jak převést virtuální počítače pomocí azure cli. Pokud ji potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Než začnete
* Projděte si [nejčastější dotazy týkající se migrace na spravované disky](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Původní virtuální pevné disky a účet úložiště používané virtuálním počítačem před převodem se neodstraní. Budou se vám za ně i nadále účtovat poplatky. Abyste se vyhnuli účtování poplatků za tyto artefakty, po ověření dokončení převodu odstraňte původní objekty blob virtuálních pevných disků. Pokud potřebujete najít tyto nepřipojené disky, abyste je odstranili, přečtěte si náš článek [Vyhledání a odstranění nepřipojených disků Azure.](find-unattached-disks.md)

## <a name="convert-single-instance-vms"></a>Převod virtuálních ms s jednou instancí
Tato část popisuje, jak převést virtuální počítače Azure s jednou instancí z nespravovaných disků na spravované disky. (Pokud jsou vaše virtuální počítače v sadě dostupnosti, přečtěte si další část.) Tento proces můžete použít k převodu virtuálních počítačů z nespravovaných disků premium (SSD) na disky spravovaných pro prémie nebo ze standardních disků (HDD) na standardní spravované disky.

1. Navrátit virtuální ho pomocí [az vm navrátit](/cli/azure/vm). Následující příklad navrací virtuální hosti s `myVM` `myResourceGroup`názvem ve skupině prostředků s názvem :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Převeďte virtuální ho na spravované disky pomocí [převodu az vm](/cli/azure/vm). Následující proces převede virtuální `myVM`hospo pojmenovaný , včetně disku operačního systému a všech datových disků:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Spusťte virtuální modul po převodu na spravované disky pomocí [spuštění virtuálního počítače az](/cli/azure/vm). Následující příklad spustí virtuální `myVM` hod pojmenovaný `myResourceGroup`ve skupině prostředků s názvem .

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Převod virtuálních aplikací v sadě dostupnosti

Pokud jsou virtuální počítače, které chcete převést na spravované disky, v sadě dostupnosti, musíte nejprve převést sadu dostupnosti na spravovanou skupinu dostupnosti.

Všechny virtuální ho disponibilní v sadě dostupnosti musí být před převodem sady dostupnosti vráceny. Naplánujte převod všech virtuálních počítačů na spravované disky poté, co byla samotná sada dostupnosti převedena na spravovanou skupinu dostupnosti. Potom spusťte všechny virtuální ho dispoziční mši a pokračujte v normálním provozu.

1. Seznam všech virtuálních discích v sadě dostupnosti pomocí [seznamu az vm dostupnost i matná sada](/cli/azure/vm/availability-set). V následujícím příkladu jsou uvedeny všechny `myAvailabilitySet` virtuální společnosti `myResourceGroup`v sadě dostupnosti pojmenované ve skupině prostředků s názvem :

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Navrátit všechny virtuální ho disponuje pomocí [az vm navrátit](/cli/azure/vm). Následující příklad navrací virtuální hosti s `myVM` `myResourceGroup`názvem ve skupině prostředků s názvem :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Převést sadu dostupnosti pomocí [az vm dostupnost-set převést](/cli/azure/vm/availability-set). Následující příklad převádí `myAvailabilitySet` sadu dostupnosti `myResourceGroup`pojmenovanou ve skupině prostředků s názvem :

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Převést všechny virtuální počítače na spravované disky pomocí [převodu az vm](/cli/azure/vm). Následující proces převede virtuální `myVM`hospo pojmenovaný , včetně disku operačního systému a všech datových disků:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Spusťte všechny virtuální počítače po převodu na spravované disky pomocí [spuštění az vm](/cli/azure/vm). Následující příklad spustí virtuální `myVM` hod pojmenovaný `myResourceGroup`ve skupině prostředků s názvem :

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Převod pomocí portálu Azure

Nespravované disky můžete také převést na spravované disky pomocí portálu Azure.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte virtuální ho virtuálního virtuálního motoru ze seznamu virtuálních discích na portálu.
3. V okně pro virtuální počítače vyberte **disky** z nabídky.
4. V horní části okna **Disky** vyberte **Migrovat na spravované disky**.
5. Pokud váš virtuální počítač je v sadě dostupnosti, bude upozornění na **migrate na spravované disky** okno, které je třeba převést sadu dostupnosti jako první. Upozornění by mělo mít odkaz, na který můžete kliknout a převést sadu dostupnosti. Jakmile se sada dostupnosti převede nebo pokud váš virtuální počítač není v sadě dostupnosti, klikněte na **Migrovat** a spusťte proces migrace disků na spravované disky.

Virtuální virtuální měsíč se po dokončení migrace zastaví a restartuje.

## <a name="next-steps"></a>Další kroky

Další informace o možnostech úložiště najdete [v tématu Přehled spravovaných disků Azure](../windows/managed-disks-overview.md).
