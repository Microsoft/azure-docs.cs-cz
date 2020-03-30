---
title: Řada NDv2 – virtuální počítače Azure
description: Specifikace pro virtuální chod řady NDv2.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8a2d275501816dd504130b255b73a752c5615f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247278"
---
# <a name="updated-ndv2-series"></a>Byla aktualizována řada NDv2

Virtuální počítač řady NDv2 je novým přírůstkem do řady GPU navržených pro potřeby nejnáročnějších úloh AI s akcelerací GPU, strojového učení, simulace a HPC úloh.

NDv2 je poháněn 8 NVIDIA Tesla V100 NVLINK připojených GPU, každý s 32 GB paměti GPU. Každý virtuální počítač NDv2 má také 40 jader Intel Xeon Platinum 8168 (Skylake) bez hyperthreadedu a 672 GiB systémové paměti.

Instance NDv2 poskytují vynikající výkon pro úlohy HPC a AI využívající výpočetní jádra optimalizovaná pro CUDA GPU a mnoho nástrojů pro umělou hmotnost, ML a analýzu, které podporují akceleraci GPU "out-of-box", jako jsou TensorFlow, Pytorch, Caffe, RAPIDS a další Rámců.

Kriticky ndv2 je vybudován pro výpočetní intenzivní škálování (využití 8 GPU na virtuální počítač) a horizontální navýšení kapacity (využití více virtuálních počítačů pracujících společně) úlohy. Řada NDv2 nyní podporuje 100gigabitové back-endové sítě InfiniBand EDR, podobné té, která je k dispozici na řadě HB virtuálního počítače HPC, aby umožnila vysoce výkonné clustering pro paralelní scénáře včetně distribuovaného školení pro AI a ML. Tato back-endová síť podporuje všechny hlavní protokoly InfiniBand, včetně těch, které používají knihovny NCCL2 společnosti NVIDIA, což umožňuje bezproblémové shlukování GPU.

> Při [povolení infinibandu](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) na ND40rs_v2 virtuálním počítači použijte ovladač 4.7-1.0.0.1 Mellanox OFED.
>
> Vzhledem ke zvýšené paměti GPU vyžaduje nový ND40rs_v2 virtuální ms použití [virtuálních zařízení generace 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) a image marketplace. 
>
> Poznámka: ND40s_v2 s 16 GB paměti na GPU již není k dispozici pro náhled a byl nahrazen aktualizovaným ND40rs_v2.

<br>

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

InfiniBand: Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GIB | GPU | Paměť GPU: GIB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální šířka pásma sítě | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000 / 800 | 24000 Mb/s | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Chcete-li využít funkce GPU virtuálních počítačů řady Azure N, musí být nainstalovány ovladače GPU NVIDIA.

[Rozšíření ovladače GPU NVIDIA](./extensions/hpccompute-gpu-linux.md) nainstaluje příslušné ovladače NVIDIA CUDA nebo GRID na virtuální počítač řady N. Nainstalujte nebo spravujte rozšíření pomocí portálu Azure nebo nástrojů, jako jsou šablony Azure PowerShellnebo Azure Resource Manager. Obecné informace o rozšířeních virtuálních virtuálních zařízení najdete v tématu [Rozšíření a funkce virtuálních strojů Azure](./extensions/overview.md).

Pokud se rozhodnete nainstalovat ovladače NVIDIA GPU ručně, přečtěte si informace [o nastavení ovladače GPU řady N pro Linux](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.
