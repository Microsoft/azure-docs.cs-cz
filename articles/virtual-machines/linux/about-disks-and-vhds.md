---
title: O nespravované (objekty BLOB stránky) a spravovaných disky úložiště pro virtuální počítače Microsoft Azure Linux | Microsoft Docs
description: Další informace o základní informace o nespravovaných (objekty BLOB stránky) a spravovaných disky úložiště pro virtuální počítače s Linuxem v Azure.
services: virtual-machines
author: roygara
manager: jeconnoc
ms.service: virtual-machines
ms.workload: storage
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.openlocfilehash: 3742b05bceea7aed556d06ab4460abaa08aca7d1
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "30286617"
---
# <a name="about-disks-storage-for-azure-linux-vms"></a>O disky úložiště pro virtuální počítače Linux Azure
Stejně jako všechny ostatní počítače virtuálních počítačů v Azure používat disky jako místo pro uložení operačního systému, aplikace a data. Všechny virtuální počítače Azure mít aspoň dva disky – disk operačního systému Linux a dočasný disk. Vytváření disku operačního systému z bitové kopie a disku operačního systému a image jsou ve skutečnosti virtuální pevné disky (VHD) uložené v účtu úložiště Azure. Virtuální počítače také může mít jeden nebo více datových disků, které jsou také uloženy jako virtuální pevné disky. 

V tomto článku jsme se v souvislosti se jiný používá pro disky a potom popisují různé typy disků můžete vytvořit a použít. Tento článek je také k dispozici pro [virtuální počítače s Windows](../windows/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Disky, které jsou používány virtuálními počítači

Podívejme se na tom, jak jsou disky používány virtuálních počítačů.

## <a name="operating-system-disk"></a>Disk operačním systému
Každý virtuální počítač má jeden disk připojené operačního systému. Je zaregistrován jako jednotka SATA a je označeno/dev/sda ve výchozím nastavení. Tento disk má maximální kapacita 2 048 gigabajtů (GB). 

## <a name="temporary-disk"></a>Dočasné disku
Každý virtuální počítač obsahuje dočasné disk. Dočasné disku poskytuje krátkodobé úložiště pro aplikace a procesy a slouží k uložení pouze data, jako jsou soubory stránky nebo odkládacího souboru. Data na dočasné disku mohou být ztraceny při [údržby](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) nebo když jste [znovu nasadit virtuální počítač](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Během restartu standardní virtuální počítač by měl uchovat data na dočasné jednotce.

Na virtuální počítače s Linuxem, disk je obvykle **/dev/sdb** a je naformátován a připojit k **/mnt** Azure Linux Agent. Velikost dočasné disku se liší, na základě velikosti virtuálního počítače. Další informace najdete v tématu [velikosti virtuálních počítačů Linux](../windows/sizes.md).

Další informace o tom, jak Azure používá dočasným diskovým najdete v tématu [pochopení dočasné jednotce ve virtuálních počítačích Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Datový disk
Datový disk je virtuální pevný disk, který je připojen k virtuálnímu počítači na ukládání dat aplikací, nebo data, která je třeba zachovat. Datové disky jsou zaregistrované jako disky SCSI a jsou označeny písmenem, který zvolíte. Každý datový disk má maximální kapacitu 4095 GB. Velikost virtuálního počítače určuje, kolik datových disků můžete připojit k jeho a typ úložiště můžete použít k hostování disky.

> [!NOTE]
> Další podrobnosti o kapacity virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů Linux](./sizes.md).
> 

Azure vytvoří disk operačního systému, když vytvoříte virtuální počítač z bitové kopie. Pokud používáte image, která obsahuje datové disky, Azure vytvoří datových disků také při vytváření virtuálního počítače. V opačném datových disků přidat po vytvoření virtuálního počítače.

Můžete přidat datových disků pro virtuální počítač v každém okamžiku nástrojem **připojení** disk k virtuálnímu počítači. Můžete vytvořit virtuální pevný disk, který jste nahráli nebo můžete zkopírovat do svého účtu úložiště nebo ten, který pro vás vytvoří Azure. Připojením datového disku přiřadí soubor virtuálního pevného disku s virtuálním Počítačem, umístěním zapůjčení na virtuální pevný disk, nelze jej odstranit z úložiště při je stále připojen.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Další postup
* [Připojit disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) přidat další úložiště pro virtuální počítač.
* [Vytvoření snímku](snapshot-copy-managed-disk.md).
* [Převést na spravované disky](convert-unmanaged-to-managed-disks.md).

