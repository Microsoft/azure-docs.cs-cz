---
title: Připojte disk k virtuálnímu počítači Azure classic | Microsoft Docs
description: Připojit datový disk k virtuálnímu počítači Windows vytvořené pomocí modelu nasazení classic a provést jeho inicializaci.
services: virtual-machines-windows, storage
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cynthn
ms.openlocfilehash: 48a6b96bd611fcc8fa8219aeef359419255b8cef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Připojení datového disku k virtuálnímu počítači s Windows vytvořenému pomocí modelu klasického nasazení

Tento článek ukazuje, jak připojit nové a stávající disky vytvořené pomocí modelu nasazení Classic do virtuálního počítače s Windows pomocí portálu Azure.



Můžete také [připojit datový disk do virtuálního počítače s Linuxem na portálu Azure](../../linux/attach-disk-portal.md).

Než připojíte disk, projděte si tyto tipy:

* Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Podrobnosti najdete v tématu [velikosti virtuálních počítačů](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Chcete-li používat úložiště úrovně Premium, je třeba DS-series nebo GS-series virtuálního počítače. Můžete použít disky z účty úložiště Premium a Standard s těchto virtuálních počítačů. Storage úrovně Premium je k dispozici v určité oblasti. Podrobnosti najdete v tématu [úložiště Premium: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Pro nový disk nemusíte nejdřív ji vytvořit, protože Azure ji vytvoří, když jeho připojení.

Můžete také [připojit datový disk pomocí prostředí Powershell](../../virtual-machines-windows-attach-disk-ps.md).

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="find-the-virtual-machine"></a>Najít virtuální počítač
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte virtuální počítač z prostředku uvedené na řídicím panelu.
3. V levém podokně v části **nastavení**, klikněte na tlačítko **disky**.

    ![Otevřete nastavení disku](./media/attach-disk/virtualmachinedisks.png)

Pokračujte podle pokynů pro připojení buď [nový disk](#option-1-attach-a-new-disk) nebo [stávající disk](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Možnost 1: Připojte a inicializace nový disk

1. Na **disky** okně klikněte na tlačítko **připojit nový**.
2. Zkontrolujte výchozí nastavení, aktualizovat podle potřeby a pak klikněte na tlačítko **OK**.

   ![Zkontrolujte nastavení disku](./media/attach-disk/attach-new.png)

3. Jakmile Azure vytvoří disk a připojí jej k virtuálnímu počítači, na nový disk, je uvedena ve nastavení disku virtuálního počítače v části **datových disků**.

### <a name="initialize-a-new-data-disk"></a>Inicializace nový datový disk

1. Připojte k virtuálnímu počítači. Pokyny najdete v tématu [jak se připojit a přihlásit se na virtuálním počítači Azure s Windows](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Po přihlášení k virtuálnímu počítači otevřete **správce serveru**. V levém podokně vyberte **Souborová služba a služba úložiště**.

    ![Otevřete správce serveru](../media/attach-disk-portal/fileandstorageservices.png)

3. Vyberte **disky**.
4. **Disky** části jsou uvedené disky. Virtuální počítač má nejčastěji disk 0, disk 1 a 2 disku. Disk 0 je disk operačního systému, disk 1 je dočasný disk a disk 2 je datový disk nově připojen k virtuálnímu počítači. Datový disk zobrazí oddíl jako **neznámé**.

 Klikněte pravým tlačítkem na disk a vyberte **inicializovat**.

5. Zobrazení upozornění, že všechna data vymažou se při inicializaci disku. Klikněte na tlačítko **Ano** upozornění na vědomí a inicializujte disk. Po dokončení oddíl bude uvedený jako **GPT**. Znovu klikněte pravým tlačítkem na disk a vyberte **nový svazek**.

6. Dokončete průvodce pomocí výchozích hodnot. Po dokončení průvodce **svazky** části je uveden seznam nového svazku. Disk je teď online a připravená k ukládání dat.

    ![Svazek úspěšně inicializován.](./media/attach-disk/newdiskafterinitialization.png)

## <a name="option-2-attach-an-existing-disk"></a>Možnost 2: Připojit stávající disk
1. Na **disky** okně klikněte na tlačítko **připojit existující**.
2. V části **připojit stávající disk**, klikněte na tlačítko **umístění**.

   ![Připojit stávající disk](./media/attach-disk/attachexistingdisksettings.png)
3. V části **účty úložiště**, vyberte účet a kontejner, který obsahuje soubor VHD.

   ![Vyhledejte umístění virtuálního pevného disku](./media/attach-disk/existdiskstorageaccountandcontainer.png)

4. Vyberte soubor VHD.
5. V části **připojit stávající disk**, právě vybraný soubor je uveden v části **souboru virtuálního pevného disku**. Klikněte na **OK**.
6. Po Azure připojí disk k virtuálnímu počítači, je uvedena v nastavení disku virtuálního počítače v části **datových disků**.

## <a name="use-trim-with-standard-storage"></a>Použít standardní úložiště uvolnění dočasné paměti

Pokud chcete použít standardní úložiště (HDD), měli byste povolit uvolnění dočasné paměti. TRIM zahodí nepoužívané bloky na disku, můžete se účtují pouze pro úložiště, které skutečně používáte. Pomocí uvolnění dočasné paměti může významně snížit náklady, včetně nepoužívané bloky, které jsou výsledkem odstranění velkých souborů.

Můžete spustit tento příkaz a zkontrolujte nastavení uvolnění dočasné paměti. Otevřete příkazový řádek na virtuální počítač s Windows a zadejte:

```
fsutil behavior query DisableDeleteNotify
```

Pokud příkaz vrátí hodnotu 0, TRIM správně povolené. Pokud vrátí hodnotu 1, spusťte následující příkaz k povolení uvolnění dočasné paměti:
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>Další postup
Pokud aplikace potřebuje používat D: disk k uložení dat, můžete [změnit písmeno jednotky dočasné disk systému Windows](../../virtual-machines-windows-change-drive-letter.md).

## <a name="additional-resources"></a>Další zdroje informací:
[O disky a virtuální pevné disky pro virtuální počítače](../../virtual-machines-linux-about-disks-vhds.md)
