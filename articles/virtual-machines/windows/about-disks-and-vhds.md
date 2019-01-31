---
title: O nespravované (objekty BLOB stránky) a spravované diskové úložiště pro virtuální počítače Windows Microsoft Azure | Dokumentace Microsoftu
description: Seznamte se se základy nespravované (objekty BLOB stránky) a spravované diskové úložiště pro virtuální počítače Windows v Azure.
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.tgt_pltfrm: windows
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 968c8aa74a35bf753d92e7c417aaec2a1361f425
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467964"
---
# <a name="about-disks-storage-for-azure-windows-vms"></a>O diskové úložiště pro virtuální počítače Azure s Windows

Stejně jako jakýkoli jiný počítač virtuální počítače v Azure používat disky jako místo pro uložení operačního systému, aplikace a data. Všechny virtuální počítače Azure obsahovat aspoň dva disky – disk operačního systému Windows a dočasný disk. Disk s operačním systémem je vytvořen z bitové kopie a disku s operačním systémem a image jsou virtuální pevné disky (VHD) uložené v účtu služby Azure storage. Virtuální počítače také může mít jeden nebo více datových disků, které jsou také uloženy jako virtuální pevné disky. 

V tomto článku jsme bude mluvit o různých disků a potom popisují různé typy disků můžete vytvořit a použít. Tento článek je také k dispozici pro [virtuální počítače s Linuxem](../linux/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Disky, které jsou používány virtuálními počítači

Pojďme se podívat, jak jsou disky používány virtuální počítače.

### <a name="operating-system-disk"></a>Disk operačním systému

Každý virtuální počítač má jeden disk připojený operačního systému. Má registrován jako jednotky SATA a označeny jako jednotku C: ve výchozím nastavení. Tento disk má maximální kapacitu 2 048 GB (Gigabajtů).

### <a name="temporary-disk"></a>Dočasný disk

Každý virtuální počítač obsahuje dočasný disk. Dočasný disk obsahuje krátkodobé úložiště pro aplikace a procesy a je určené k ukládání pouze data, jako jsou stránkovací nebo odkládací soubory. Data na dočasném disku mohou být ztracena během [události údržby](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) nebo když jste [opětovné nasazení virtuálního počítače](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Při úspěšném standardní restartování virtuálního počítače byste neměli zachovat data na dočasné jednotce. Existují však případy, kde nemusí uchovávat data, jako je například přesun do nového hostitele. Odpovídajícím způsobem všechna data na dočasné jednotky by neměl být data, která jsou zásadní pro systém.

Dočasný disk je označena jako jednotku D: ve výchozím nastavení a to se používá k ukládání pagefile.sys. Pokud chcete přemapovat tento disk na jiné písmeno jednotky, přečtěte si téma [změnit písmeno jednotky dočasného disku Windows](change-drive-letter.md). Velikost dočasného disku se liší, na základě velikosti virtuálního počítače. Další informace najdete v tématu [Windows pro velikosti virtuálních počítačů](sizes.md).

Další informace o tom, jak Azure používá dočasný disk najdete v tématu [pochopení dočasné jednotky na virtuálních počítačích Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

### <a name="data-disk"></a>Datový disk

Datový disk je virtuální pevný disk, který je připojen k virtuálnímu počítači k ukládání dat aplikací nebo data, která je potřeba nechat. Datové disky jsou registrovány jako disky SCSI a jsou označeny písmeno, kterou zvolíte. Každý datový disk má maximální kapacitu 4 095 GB, spravované disky mají maximální kapacita 32 767 GiB. Velikost virtuálního počítače určuje, kolik datových disků můžete připojit a typ úložiště můžete použít k hostování disky.

> [!NOTE]
> Další informace o virtuálních počítačích kapacity najdete v tématu [Windows pro velikosti virtuálních počítačů](sizes.md).

Azure vytvoří disk s operačním systémem, když vytváříte virtuální počítač z bitové kopie. Pokud použijete image, která obsahuje datové disky, Azure vytvoří datové disky také při vytváření virtuálního počítače. V opačném případě přidáte datových disků po vytvoření virtuálního počítače.

Můžete přidat datové disky na virtuální počítač v každém okamžiku podle **připojení** disku k virtuálnímu počítači. Můžete použít virtuální pevný disk, který jste nahráli nebo zkopírovat do svého účtu úložiště, nebo použít prázdný virtuální pevný disk, který pro vás vytvoří Azure. Připojení datového disku přidruží k souboru virtuálního pevného disku virtuálního počítače tak, že "zapůjčení na virtuální pevný disk, proto ji nelze odstranit z úložiště je pořád připojený.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

Velikosti ve verzi preview najdete v tématu naše [nejčastější dotazy k](faq-for-disks.md#new-disk-sizes-managed-and-unmanaged) se dozvíte, jaké oblasti jsou k dispozici v.

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>Jedním z poslední doporučení: Pomocí operace TRIM na nespravované disky standard

Pokud používáte nespravované disky úrovně standard (HDD), měli byste povolit uvolnění dočasné paměti. TRIM zahodí nepoužívané bloky na disku tak, že nebudete dostávat faktury za úložiště, které skutečně používáte. To můžete uložit na náklady, pokud vytvoříte velkých souborů a potom je odstraňte.

Můžete spustit tento příkaz a zkontrolujte nastavení uvolnění dočasné paměti. Otevřete příkazový řádek na virtuální počítač Windows a zadejte:

```
fsutil behavior query DisableDeleteNotify
```

Pokud příkaz vrátí hodnotu 0, uvolnění dočasné paměti je povolená správně. Pokud vrátí hodnotu 1, spuštěním následujícího příkazu povolte uvolnění dočasné paměti:

```
fsutil behavior set DisableDeleteNotify 0
```

> [!NOTE]
> Poznámka: Podpora uvolnění dočasné paměti spustí s Windows serverem 2012 nebo Windows 8 a vyšším, viz [nové rozhraní API umožňuje aplikacím odesílat "TRIM a rušení mapování" pomocné parametry úložná média](https://msdn.microsoft.com/windows/compatibility/new-api-allows-apps-to-send-trim-and-unmap-hints).
> 

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>Další postup
* [Připojení disku](attach-disk-portal.md) přidat další úložiště pro virtuální počítač.
* [Vytvoření snímku](snapshot-copy-managed-disk.md).
* [Převod na managed disks](convert-unmanaged-to-managed-disks.md).


