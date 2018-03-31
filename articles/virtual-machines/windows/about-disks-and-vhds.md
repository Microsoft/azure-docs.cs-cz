---
title: O nespravované (objekty BLOB stránky) a spravovaných disky úložiště pro virtuální počítače Windows Microsoft Azure | Microsoft Docs
description: Další informace o základní informace o nespravovaných (objekty BLOB stránky) a spravovaných disky úložiště pro virtuální počítače s Windows v Azure.
services: virtual-machines
author: roygara
manager: jeconnoc
ms.service: virtual-machines
ms.workload: storage
ms.tgt_pltfrm: windows
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.openlocfilehash: 3d82416b7382b8e09706fd7fb8af9e1d3fe79f01
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="about-disks-storage-for-azure-windows-vms"></a>O disky úložiště pro virtuální počítače Windows Azure
Stejně jako všechny ostatní počítače virtuálních počítačů v Azure používat disky jako místo pro uložení operačního systému, aplikace a data. Všechny virtuální počítače Azure mít aspoň dva disky – disk operačního systému Windows a dočasný disk. Vytváření disku operačního systému z bitové kopie a disku operačního systému a image jsou virtuální pevné disky (VHD) uložené v účtu úložiště Azure. Virtuální počítače také může mít jeden nebo více datových disků, které jsou také uloženy jako virtuální pevné disky. 

V tomto článku jsme se v souvislosti se jiný používá pro disky a potom popisují různé typy disků můžete vytvořit a použít. Tento článek je také k dispozici pro [virtuální počítače s Linuxem](../linux/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Disky, které jsou používány virtuálními počítači

Podívejme se na tom, jak jsou disky používány virtuálních počítačů.

### <a name="operating-system-disk"></a>Disk operačním systému
Každý virtuální počítač má jeden disk připojené operačního systému. Má registrován jako jednotky SATA a označené jako jednotky C: ve výchozím nastavení. Tento disk má maximální kapacita 2 048 gigabajtů (GB). 

### <a name="temporary-disk"></a>Dočasné disku
Každý virtuální počítač obsahuje dočasné disk. Dočasné disku poskytuje krátkodobé úložiště pro aplikace a procesy a slouží k uložení pouze data, jako jsou soubory stránky nebo odkládacího souboru. Data na dočasné disku mohou být ztraceny při [údržby](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) nebo když jste [znovu nasadit virtuální počítač](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Během restartu standardní virtuální počítač by měl uchovat data na dočasné jednotce.

Dočasné disku je označený jako jednotka D: výchozí a používá se pro ukládání pagefile.sys. Přemapování tento disk k jiné písmeno jednotky, najdete v části [změnit písmeno jednotky dočasné disk systému Windows](change-drive-letter.md). Velikost dočasné disku se liší, na základě velikosti virtuálního počítače. Další informace najdete v tématu [virtuální počítače s velikostí pro Windows](sizes.md).

Další informace o tom, jak Azure používá dočasným diskovým najdete v tématu [pochopení dočasné jednotce ve virtuálních počítačích Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)


### <a name="data-disk"></a>Datový disk
Datový disk je virtuální pevný disk, který je připojen k virtuálnímu počítači na ukládání dat aplikací, nebo data, která je třeba zachovat. Datové disky jsou zaregistrované jako disky SCSI a jsou označeny písmenem, který zvolíte. Každý datový disk má maximální kapacitu 4095 GB. Velikost virtuálního počítače určuje, kolik datových disků můžete připojit k jeho a typ úložiště můžete použít k hostování disky.

> [!NOTE]
> Další informace o virtuální počítače kapacity najdete v tématu [virtuální počítače s velikostí pro Windows](sizes.md).
> 

Azure vytvoří disk operačního systému, když vytvoříte virtuální počítač z bitové kopie. Pokud používáte image, která obsahuje datové disky, Azure vytvoří datových disků také při vytváření virtuálního počítače. V opačném datových disků přidat po vytvoření virtuálního počítače.

Můžete přidat datových disků pro virtuální počítač v každém okamžiku nástrojem **připojení** disk k virtuálnímu počítači. Můžete použít virtuální pevný disk, který jste nahráli nebo můžete zkopírovat do svého účtu úložiště, nebo použijte prázdný virtuální pevný disk, který pro vás vytvoří Azure. Připojením datového disku přiřadí soubor virtuálního pevného disku s virtuálním Počítačem umístěním zapůjčení na virtuální pevný disk, nelze jej odstranit z úložiště při je stále připojen.


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>Jedním z poslední doporučení: použití TRIM s nespravované standardní disky 

Pokud používáte nespravované standardní disky (HDD), měli byste povolit uvolnění dočasné paměti. TRIM zahodí nepoužívané bloky na disku, můžete se účtují pouze pro úložiště, které skutečně používáte. To můžete uložit na náklady, pokud chcete vytvořit velkých souborů a pak odstraňte je. 

Můžete spustit tento příkaz a zkontrolujte nastavení uvolnění dočasné paměti. Otevřete příkazový řádek na virtuální počítač s Windows a zadejte:


```
fsutil behavior query DisableDeleteNotify
```

Pokud příkaz vrátí hodnotu 0, TRIM správně povolené. Pokud vrátí hodnotu 1, spusťte následující příkaz k povolení uvolnění dočasné paměti:

```
fsutil behavior set DisableDeleteNotify 0
```

> [!NOTE]
> Poznámka: Podpora uvolnění dočasné paměti spustí s Windows serverem 2012 nebo Windows 8 a vyšší, najdete v tématu [nové rozhraní API umožňuje aplikacím odeslat "TRIM a zrušit mapování" pomocné parametry úložná média,](https://msdn.microsoft.com/windows/compatibility/new-api-allows-apps-to-send-trim-and-unmap-hints).
> 

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>Další postup
* [Připojit disk](attach-disk-portal.md) přidat další úložiště pro virtuální počítač.
* [Vytvoření snímku](snapshot-copy-managed-disk.md).
* [Převést na spravované disky](convert-unmanaged-to-managed-disks.md).


