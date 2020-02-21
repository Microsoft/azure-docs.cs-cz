---
title: NDv2-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady NDv2-Series.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 79e7f2a8dad59de4dc9d84a26d24c82889c14c2f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493316"
---
# <a name="updated-ndv2-series-preview"></a>Aktualizace NDv2-Series (Preview)

Virtuální počítač řady NDv2-Series je novým doplňkem k rodině GPU navržené pro potřeby nejnáročnějších úloh AI, strojového učení, simulace a prostředí HPC s podporou GPU.

NDv2 je napájený pomocí 8 NVIDIA Tesla V100 NVLINK – připojené GPU s 32 GB paměti GPU. Každý virtuální počítač s NDv2 má taky 40 jader Intel Xeon Platinum 8168 (Skylake) 672, které neobsahují GiB systémové paměti.

Instance NDv2 poskytují vynikající výkon pro úlohy HPC a AI s využitím CUDA výpočetních jader optimalizovaných pro GPU a mnoha nástrojů AI, ML a Analytics, které podporují akceleraci GPU, jako je například TensorFlow, Pytorch, Caffe, RAPIDS a další. rozhraní.

Kriticky je NDv2 sestavena jak pro výpočetně náročné škálování (což znamená 8 GPU na virtuální počítač), tak na škálování (více vzájemně pracujících více virtuálních počítačů) úloh. NDv2 Series teď podporuje 100 gigabitové sítě InfiniBand EDR, které jsou k dispozici na začátku série virtuálních počítačů HPC, a umožňují tak vysoce výkonné clustering pro paralelní scénáře, včetně distribuovaného školení pro AI a ML. Tato síť back-end podporuje všechny hlavní protokoly InfiniBand, včetně těch, které pracují s knihovnami NCCL2 společnosti NVIDIA, což umožňuje bezproblémové clusteringu GPU.

> Při [povolování InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) na virtuálním počítači s ND40rs_v2 použijte prosím ovladač OFED 4.7-1.0.0.1 Mellanox.

> V důsledku zvýšené paměti GPU vyžaduje nový virtuální počítač ND40rs_v2 použití [virtuálních počítačů generace 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) a imagí z Marketplace. 

> [Přihlaste se, abyste si vyžádali předběžný přístup k virtuálnímu počítači NDv2 ve verzi Preview.](https://aka.ms/AzureNDrv2Preview)

> Poznámka: ND40s_v2 o velikosti 16 GB na paměť GPU již není k dispozici pro verzi Preview a byla nahrazena aktualizovaným ND40rs_v2.
<br>

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

InfiniBand: podporované

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální šířka pásma sítě | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 16 | 32 | 80000 / 800 | 24000 MB/s | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Aby bylo možné využít možnosti GPU pro virtuální počítače řady Azure N-Series, je nutné nainstalovat ovladače GPU NVIDIA.

[Rozšíření ovladače NVIDIA GPU](/extensions/hpccompute-gpu-windows.md) nainstaluje vhodné ovladače NVIDIA CUDA nebo Grid na virtuální počítač řady N-Series. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako jsou šablony Azure PowerShell nebo Azure Resource Manager. Podporované operační systémy a kroky nasazení najdete v [dokumentaci k rozšíření ovladače GPU NVIDIA](/extensions/hpccompute-gpu-windows.md) . Obecné informace o rozšířeních virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních počítačů Azure](/extensions/overview.md).

Pokud se rozhodnete nainstalovat ovladače GPU NVIDIA ručně, přečtěte si téma Instalace ovladače GPU [řady n-Series pro Windows](/windows/n-series-driver-setup.md) nebo [n-Series Setup Driver Setup pro Linux](/linux/n-series-driver-setup) pro podporované operační systémy, ovladače, instalace a postupy ověření.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.