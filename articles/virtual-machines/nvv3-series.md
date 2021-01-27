---
title: NVv3-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady NVv3-Series.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: ceefcb41dec1ce40d55f412ba51cb1b2cc02daea
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919693"
---
# <a name="nvv3-series"></a>Řada NVv3

Virtuální počítače řady NVv3-Series využívají technologii [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a technologii NVIDIA Grid s procesory Intel E5-2690 v4 (Broadwell) a technologií Intel Hyper-Threading. Tyto virtuální počítače jsou určené pro akcelerované grafické aplikace GPU a virtuální plochy, kde si zákazníci chtějí vizualizovat svá data, simulovat výsledky pro zobrazení, práci na CAD nebo vykreslování a streamování obsahu. Tyto virtuální počítače navíc můžou spouštět úlohy s jednoduchou přesností, jako je například kódování a vykreslování. Virtuální počítače s NVv3 podporují Premium Storage a přidávají se s dvojnásobnou paměťovou pamětí (RAM) ve srovnání s jejím předchůdcem (řady NV).  

Každý grafický procesor v NVv3 instancích obsahuje licenci na MŘÍŽKu. Tato licence vám poskytne flexibilitu pro použití instance NV jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů se může připojit k virtuálnímu počítači pro scénář virtuální aplikace.

[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované<br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) | Virtuální pracovní stanice | Virtuální aplikace |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_NV12s_v3 |12 | 112 | 320  | 1 | 8  | 12 | 20000/200 | 4 / 6000 | 1 | 25  |
| Standard_NV24s_v3 |24 | 224 | 640  | 2 | 16 | 24 | 40000/400 | 8 / 12000 | 2 | 50  |
| Standard_NV48s_v3 |48 | 448 | 1280 | 4 | 32 | 32 | 80000/800 | 8 / 24000 | 4 | 100 |

<sup>1</sup> 1 GPU = jedna polovina M60 karta.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Aby bylo možné využít možnosti GPU pro virtuální počítače řady Azure N-Series, je nutné nainstalovat ovladače GPU NVIDIA.

[Rozšíření ovladače NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) nainstaluje vhodné ovladače NVIDIA CUDA nebo Grid na virtuální počítač řady N-Series. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako jsou šablony Azure PowerShell nebo Azure Resource Manager. Podporované operační systémy a kroky nasazení najdete v [dokumentaci k rozšíření ovladače GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) . Obecné informace o rozšířeních virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních počítačů Azure](./extensions/overview.md).

Pokud se rozhodnete nainstalovat ovladače GPU NVIDIA ručně, přečtěte si téma Instalace ovladače GPU [řady n-Series pro Windows](./windows/n-series-driver-setup.md) nebo [n-Series Setup Driver Setup pro Linux](./linux/n-series-driver-setup.md) pro podporované operační systémy, ovladače, instalace a postupy ověření.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU.](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
