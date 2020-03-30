---
title: Řada ND – virtuální počítače Azure
description: Specifikace pro virtuální chody řady ND.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 163dc0d6ecd4f91273a82b04ce186f7a8df868af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465052"
---
# <a name="nd-series"></a>Řada ND

Virtuální počítače řady ND jsou novým přírůstkem do řady GPU navržených pro úlohy AI a Deep Learning. Nabízejí vynikající výkon pro školení a závěry. Nd instance jsou poháněny [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU a Intel Xeon E5-2690 v4 (Broadwell) procesory. Tyto instance poskytují vynikající výkon pro operace s plovoucí desetinnou desetinnou desetinnou a malou a malou a malou a malou a druhou, pro úlohy AI využívající sadu Microsoft Cognitive Toolkit, TensorFlow, Caffe a další architektury. Řada ND také nabízí mnohem větší velikost paměti GPU (24 GB), což umožňuje umístit mnohem větší modely neuronových sítí. Stejně jako řada NC nabízí řada ND konfiguraci se sekundární sítí s nízkou latencí a vysokou propustností prostřednictvím rdma a připojením InfiniBand, takže můžete spouštět rozsáhlé školicí úlohy zahrnující mnoho gpu.

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

> [!IMPORTANT]
> Pro tuto řadu virtuálních počítačových společností je kvóta virtuálních procesorů (jádra) na oblast ve vašem předplatném zpočátku nastavena na 0. [Požádejte o zvýšení kvóty virtuálního procesoru](../azure-supportability/resource-manager-core-quotas-request.md) pro tuto řadu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>
| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS/MBps | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = jedna karta P40.

*Podpora RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Chcete-li využít funkce GPU virtuálních počítačů řady Azure N, musí být nainstalovány ovladače GPU NVIDIA.

[Rozšíření ovladače GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) nainstaluje příslušné ovladače NVIDIA CUDA nebo GRID na virtuální počítač řady N. Nainstalujte nebo spravujte rozšíření pomocí portálu Azure nebo nástrojů, jako jsou šablony Azure PowerShellnebo Azure Resource Manager. Informace o podporovaných operačních systémech a krocích nasazení naleznete v [dokumentaci k rozšíření ovladače GPU NVIDIA.](./extensions/hpccompute-gpu-windows.md) Obecné informace o rozšířeních virtuálních virtuálních zařízení najdete v tématu [Rozšíření a funkce virtuálních strojů Azure](./extensions/overview.md).

Pokud se rozhodnete nainstalovat ovladače GPU NVIDIA ručně, přečtěte si informace [o nastavení ovladače GPU řady N pro Windows](./windows/n-series-driver-setup.md) nebo [n-series pro Linux](./linux/n-series-driver-setup.md) pro podporované operační systémy, ovladače, instalaci a kroky ověření.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.
