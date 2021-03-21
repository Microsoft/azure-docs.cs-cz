---
title: Msv2-Series (Preview) – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady Msv2-Series.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: a7f4757467523837423d52998eb6b8204090e627
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562567"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Střední paměť Msv2 a Mdsv2-Series (Preview)


> [!IMPORTANT]
> Zapojte se do náhledu vyplněním formuláře na **https://aka.ms/Mv2MedMemoryPreview** .  

Série virtuálních počítačů Msv2 a Mdsv2 paměti Intel® Xeon® Platinum 8280 (Cascade Lake) procesor se základní frekvencí jádra 2,7 GHz a 4,0 GHz single core. U těchto virtuálních počítačů zákazníci dosahují větší flexibility s využitím místních disků a možností bez disků. Zákazníci mají také přístup k sadě nových izolovaných velikostí virtuálních počítačů s větším množstvím PROCESORů a paměti, které 192 vCPU paměti a 4 TiB paměti. 


Virtuální počítače s Msv2 a Mdsv2-Series jsou jenom generace 2 a podporují podmnožinu podporovaných imagí 2. generace. Úplný seznam podporovaných imagí pro Msv2 a Mdsv2-Series najdete níže.  

- Windows Server 2019 nebo novější
- SUSE Linux Enterprise Server 12 SP4 a novější nebo SUSE Linux Enterprise Server 15 SP1 a novější
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 nebo novější 
- Oracle Enterprise Linux 7,7 nebo novější

Další informace o virtuálních počítačích 2. generace najdete v tématu [Podpora pro virtuální počítače 2. generace v Azure](./generation-2.md).



[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 2<br>
[Akcelerátor zápisu](./how-to-enable-write-accelerator.md): podporováno<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované<br>
[Dočasné disky s operačním systémem](ephemeral-os-disks.md): nepodporované <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 střední paměť bezdiskového disku 

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet | Očekávaná šířka pásma sítě (MB/s) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1 792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2 048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2 048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4 096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Mdsv2 střední paměť s použitím disku  

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální datový disk | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet | Očekávaná šířka pásma sítě (MB/s) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1024 | 32 | 40000/400 | 20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 2 048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1 792 | 2 048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2 048 | 4 096 | 64 |160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4 096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2 048 | 4 096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4 096 | 4 096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Další velikosti a informace

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU.](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

Cenová kalkulačka: [Cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
