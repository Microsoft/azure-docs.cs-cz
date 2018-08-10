---
title: O nespravované (objekty BLOB stránky) a spravované diskové úložiště pro Microsoft Azure s Linuxem | Dokumentace Microsoftu
description: Seznamte se se základy nespravované (objekty BLOB stránky) a spravované diskové úložiště pro virtuální počítače s Linuxem v Azure.
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.component: disks
ms.openlocfilehash: feb3e60ee1b43ec85c81912fbce086858bb33742
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715926"
---
# <a name="about-disks-storage-for-azure-linux-vms"></a>O diskové úložiště pro virtuální počítače Azure s Linuxem
Stejně jako jakýkoli jiný počítač virtuální počítače v Azure používat disky jako místo pro uložení operačního systému, aplikace a data. Všechny virtuální počítače Azure obsahovat aspoň dva disky – disk operačního systému Linux a dočasný disk. Disk s operačním systémem je vytvořen z bitové kopie a disku s operačním systémem a image jsou virtuální pevné disky (VHD) uložené v účtu služby Azure storage. Virtuální počítače také může mít jeden nebo více datových disků, které jsou také uloženy jako virtuální pevné disky.

V tomto článku jsme bude mluvit o různých disků a potom popisují různé typy disků můžete vytvořit a použít. Tento článek je také k dispozici pro [Windows virtual machines](../windows/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Disky, které jsou používány virtuálními počítači

Pojďme se podívat, jak jsou disky používány virtuální počítače.

## <a name="operating-system-disk"></a>Disk operačním systému

Každý virtuální počítač má jeden disk připojený operačního systému. Je zaregistrovaný jako jednotky SATA a je označená/dev/sda ve výchozím nastavení. Tento disk má maximální kapacitu 2 048 GB (Gigabajtů).

## <a name="temporary-disk"></a>Dočasný disk

Každý virtuální počítač obsahuje dočasný disk. Dočasný disk obsahuje krátkodobé úložiště pro aplikace a procesy a je určené k ukládání pouze data, jako jsou stránkovací nebo odkládací soubory. Data na dočasném disku mohou být ztracena během [události údržby](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) nebo když jste [opětovné nasazení virtuálního počítače](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Během standardní restartování virtuálního počítače byste neměli zachovat data na dočasné jednotce. Existují však případy, kde nemusí uchovávat data, jako je například přesun do nového hostitele. Odpovídajícím způsobem všechna data na dočasné jednotky by neměl být data, která jsou zásadní pro systém.

Na virtuální počítače s Linuxem, tento disk je obvykle **/dev/sdb** a je ve formátu a připojený k **/mnt** pomocí agenta Azure Linux. Velikost dočasného disku se liší, na základě velikosti virtuálního počítače. Další informace najdete v tématu [velikostí pro virtuální počítače s Linuxem](../windows/sizes.md).

Další informace o tom, jak Azure používá dočasný disk najdete v tématu [pochopení dočasné jednotky na virtuálních počítačích Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Datový disk

Datový disk je virtuální pevný disk, který je připojen k virtuálnímu počítači k ukládání dat aplikací nebo data, která je potřeba nechat. Datové disky jsou registrovány jako disky SCSI a jsou označeny písmeno, kterou zvolíte. Každý datový disk má maximální kapacitu 4 095 GB. Velikost virtuálního počítače určuje, kolik datových disků můžete připojit a typ úložiště můžete použít k hostování disky.

> [!NOTE]
> Další informace o virtuálních počítačích kapacity najdete v tématu [velikostí pro virtuální počítače s Linuxem](./sizes.md).

Azure vytvoří disk s operačním systémem, když vytváříte virtuální počítač z bitové kopie. Pokud použijete image, která obsahuje datové disky, Azure vytvoří datové disky také při vytváření virtuálního počítače. V opačném případě přidáte datových disků po vytvoření virtuálního počítače.

Můžete přidat datové disky na virtuální počítač v každém okamžiku podle **připojení** disku k virtuálnímu počítači. Můžete použít virtuální pevný disk, který jste nahráli nebo zkopírovat do svého účtu úložiště nebo ten, který pro vás vytvoří Azure. Připojení datového disku přidruží k souboru virtuálního pevného disku virtuálního počítače, tak, že "zapůjčení na virtuální pevný disk, proto ji nelze odstranit z úložiště je pořád připojený.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Další postup
* [Připojení disku](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) přidat další úložiště pro virtuální počítač.
* [Vytvoření snímku](snapshot-copy-managed-disk.md).
* [Převod na managed disks](convert-unmanaged-to-managed-disks.md).

